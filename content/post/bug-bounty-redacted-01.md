---
title: "[Writeup][Bug Bounty][Redacted] No Rate Limit in Forgot Password [ID]"
date: '2019-11-15 05:00:00'
tags: ['bugbounty','redacted','writeup']
categories: ['writeup','bugbounty']
author: "fadhilthomas"
draft: false
---

No Rate Limit adalah sebuah kondisi di mana sebuah aplikasi tidak memiliki pembatasan terhadap request yang sama. Jadi seseorang dapat mengirimkan request sama yang berulang-ulang.

----
## Affected Endpoint 
```
https://[redacted]/api/internal/auth/reset_password_email
```

----
## Impact
Karena service yang tidak memiliki rate limit berhubungan dengan layanan email, maka seseorang dapat membuat service ini mengirimkan email reset password secara masif. Hal ini dapat membuat reputasi pengirim email menjadi buruk dan dinilai sebagai spammer oleh Email Provider seperti Gmail.

----
## Steps to Reproduce 
* Lakukan reset password untuk mendapatkan request.
* Setelah request didapat, ulangi request dengan tool Burp Suite.
* Pilih request yang ingin diulangi, kemudian kirim ke Intruder.
* Hapus semua payload position, kemudian ubah tipe Payload menjadi Null Payload.
* Kemudian dalam Payload Option, pilih Continue Indefinitely.
* Pilih Start Attack untuk mengirimkan request terus menerus.
* Periksa inbox email untuk melihat jumlah email reset password yang masuk sesuai dengan jumlah request yang dikirim.

----
## Timeline
* 03 Oct 2019 : Sent the report.
* 07 Oct 2019 : The report is triaged.
* 02 Nov 2019 : $200 sent as reward.