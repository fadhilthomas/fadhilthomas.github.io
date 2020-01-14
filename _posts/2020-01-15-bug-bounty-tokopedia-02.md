---
title: "[Writeup][Bug Bounty][Tokopedia] Information Disclosure of Sensitive Information pada Verification Login Page [ID]"
layout: post
date: '2020-01-15 18:03:56'
headerImage: false
star: true
tag:
- bugbounty
- tokopedia
- writeup
category: blog
author: fadhilthomas
---

<p align="center">
  <img src="https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img1.jpg">
</p>

## Intro
Setelah melakukan login, kita akan dialihkan ke halaman verifikasi dengan dua opsi yaitu SMS dan Telepon. Nomor telepon yang digunakan untuk mengirimkan kode verifikasi tidak diperlihatkan seluruhnya untuk alasan keamanan. Akan tetapi informasi pengguna tersimpan di dalam Cookie dan hanya dilakukan URL-encoding.

----
## Affected Endpoint 
https://accounts.tokopedia.com/otp/c/page?d=Wed%2C+13+Feb+2019+11%3A11%3A19+%2B0700&h=h4YnAMzDb4zAVjThQXtGbxa2C7Hv80Fk4TAUXe%2BvGqk%3D&ld=https%3A%2F%2Faccounts.tokopedia.com%2Fotp%2Finterrupt%2Fsl&otp_type=13

----
## Impact
1. Apabila kredential pengguna bocor dan seseorang mencoba untuk melakukan login, walaupun dia belum melewati halaman verifikasi tetapi dia sudah bisa mendapatkan informasi sensitif pengguna seperti nama, nomor telepon, email, tanggal pendaftaran dan ulang tahun yang dapat digunakan untuk melakukan phishing untuk mencuri kode verifikasi.

----
## Steps to Reproduce 
* Login ke Tokopedia.
<br/>
<br/>
* Pilih salah satu verifikasi, intercept request.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia02/img1.png)
<br/>
<br/>
* Dalam parameter badan request, email dan nomor telepon ditutupi tetapi ada informasi menarik dalam cookie
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia02/img2.jpg)
<br/>
<br/>
* Coba URL decode informasi tersebut. Terbukti informasi tersebut adalah informasi sensitif pengguna seperti: nama, nomor telepon, email, tanggal pendaftaran, dan ulang tahun.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia02/img3.jpg)
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia02/img4.jpg)
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia02/img5.jpg)

----
## Timeline
* 13 Feb 2019 : Melaporkan ke Tokopedia.
* 13 Feb 2019 : Tokopedia menerima laporan.
* 13 Feb 2019 : Tokopedia menyatakan tidak valid.
* 15 Apr 2019 : Bug telah diperbaiki.

----
## References
1. http://cwe.mitre.org/data/definitions/200.html
2. http://projects.webappsec.org/w/page/13246936/Information Leakage