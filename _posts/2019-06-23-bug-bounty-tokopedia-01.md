---
title: "[Writeup][Bug Bounty][Tokopedia] Manipulasi Jumlah Likes di Ulasan Produk [ID]"
layout: post
date: '2019-06-23 18:03:56'
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

## Pendahuluan
Sebelum mulai ke pembahasan, pertama kita bahas dulu apa itu IDOR. Insecure Direct Object Reference yang selanjutnya disebut IDOR adalah suatu kondisi di mana pengguna dapat mengakses suatu objek tanpa melewati pemeriksaan hak akses. (OWASP, 2019)

Dengan celah IDOR, seorang pengguna dapat melakukan pengaksesan, pengubahan, serta penghapusan suatu data. Hal ini membuat IDOR termasuk celah keamanan yang sangat berbahaya.

Baiklah, setelah mengetahui apa itu IDOR, kita lanjut ke pembahasan mengenai bug ini. Perlu diketahui, bug yang dibahas pada writeup ini sudah dipatch oleh pihak Tokopedia dan screenshot akan disensor karena PII.

----
## Affected Endpoint 
https://ws.tokopedia.com/reputationapp/review/api/v1/likedislike

----
## Impact
Manipulasi jumlah likes pada Ulasan Produk

----
## Steps to Reproduce 
* Login ke dalam akun Tokopedia dan buka halaman ulasan suatu produk.
<br/>
<br/>
* Intercept koneksi request, klik tombol like suatu review.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img2.jpg)
<br/>
<br/>
* Dalam koneksi request yang ter-intercept, ada beberapa parameter yaitu:
**product_id** adalah id product yang direview,
**shop_id** adalah id toko, dan
**user_id** adalah id user yang melakukan like.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img3.jpg)
<br/>
<br/>
* Forward request, kemudian mendapat balasan success.
<br/>
<br/>
* Untuk mencoba melakukan manipulasi yaitu menambah jumlah like yaitu dengan mengganti id user dengan id user lain, tanpa perlu interaksi user tersebut. Ubah user_id dengan id user lain dan hapus beberapa parameter untuk melewati autentikasi user.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img4.jpg)
<br/>
<br/>
* Forward request lagi.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img5.jpg)
<br/>
<br/>
* Jumlah like bertambah.
<br/>
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img6.jpg)
<br/>
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img7.jpg)

----
## Timeline
* 23 Feb 2019 : Melaporkan ke Tokopedia.
* 23 Feb 2019 : Tokopedia menerima laporan.
* 25 Feb 2019 : Tokopedia menyatakan valid dengan severity Medium.
* 01 Apr 2019 : Bug telah diperbaiki.
* 22 May 2019 : Reward dikirim.

----
## References
1. https://www.owasp.org/index.php/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet
2. https://www.owasp.org/index.php/Testing_for_Insecure_Direct_Object_References_(OTG-AUTHZ-004)
3. https://www.bugcrowd.com/how-to-find-idor-insecuredirect-object-reference-vulnerabilities-for-large-bountyrewards/