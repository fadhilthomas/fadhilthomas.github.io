---
title: "Mencoba Falco - Cloud-Native Runtime Security di Amazon EKS"
date: '2022-08-14 00:00:00'
tags: ['falco','cloud-native','explore']
categories: ['explore','cloud-native']
author: "fadhilthomas"
draft: false
---

![alt text](/falco01/falco-logo.webp)

Tulisan ini saya buat sebagai catatan saat mencoba Falco untuk memantau *container* secara *runtime* di Amazon EKS. 

## Apa itu Falco?
Falco adalah aplikasi *runtime security* yang *open-source* dan gratis dikembangkan oleh Sysdic, Inc. Saat tulisan ini dibuat, Falco masuk ke dalam CNCF project dengan status inkubasi.

## Apa manfaat menggunakan Falco?

Falco dapat mendeteksi dan mengirimkan notifikasi apabila ada aktifitas di dalam *container* yang dianggap membahayakan sesuai dengan aturan yang sudah dibuat sebelumnya. Falco dapat memantau system call Linux dengan menggunakan kernel module atau eBPF probe dari kernel secara *runtime*.

Falco memiliki beberapa *rule* bawaan, diantara:
* *Privilege escalation* menggunakan *privileged containers*
* Perubahan Namespace menggunakan aplikasi seperti setns
* Aktifitas baca atau tulis ke direktori terkenal seperti `/etc`, `/usr/bin`, `/usr/sbin`, dan lain-lain
* Pembuatan `symlinks`
* Perubahan `ownership` dan `mode`
* Koneksi jaringan yang tidak terduga atau perubahan *socket*
* Memunculkan processes menggunakan `execve`
* Mengeksekusi shell binaries seperti `sh`, `bash`, `csh`, `zsh`, dan lain-lain
* Mengeksekusi SSH binaries seperti `ssh`, `scp`, `sftp`, dan lain-lain
* Perubahan Linux `coreutils` executables
* Perubahan login binaries
* Perubahan `shadowutil` or `passwd` executables seperti `shadowconfig`, `pwck`, `chpasswd`, `getpasswd`, `change`, `useradd`, dan lain-lain.

Mungkin itu sampai di sana saja penjelasan singkat untuk Falco, untuk yang ingin tahu lebih detil bisa lihat dokumentasi falco di https://falco.org/docs/ dan daftar lengkap *rule* bisa lihat di https://github.com/falcosecurity/falco/tree/master/rules.

## Bagaimana memasang Falco?

Sebelum dapat memasang Falco, terlebih dahulu saya men*deploy* sebuah kluster Kubernetes. Di tulisan ini, saya memilih Amazon EKS dengan bantuan aplikasi `eksctl` dengan CloudFormation. 


## Referensi
1. https://falco.org/docs/getting-started/
2. https://eksctl.io/