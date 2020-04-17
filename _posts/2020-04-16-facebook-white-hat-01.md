---
title: "[Writeup][Bug Bounty][Instagram] Instagram Still Send New DMs and Video Calls to Device After Logout [ID][EN]"
layout: post
date: '2020-04-16 11:00:00'
headerImage: false
star: true
tag:
- bugbounty
- instagram
- facebook
- writeup
category: blog
author: fadhilthomas
---

<p align="center">
  <img src="https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/facebook01/instagram_logo.png">
</p>

## Description
Setelah pengguna melakukan *logout* akunnya, *session* seharusnya di *invalidate* untuk menghentikan aksi yang membutuhkan otentikasi seperti menerima notifikasi pesan yang masuk. Pada postingan ini membahas tentang Instagram yang menampilkan notifikasi pesan masuk walaupun pengguna telah mengakhiri *session* akunnya.
<br/>
<br/>
<span class="evidence">
*After the user logs out of his account, the session should be invalidated to stop actions that require authentication such as receiving notifications of incoming messages. This post is about Instagram, which shows incoming message notifications even though the user has ended his account session.*
</span>
----
## Impact
*Attacker* masih bisa mendapatkan notifikasi pesan yang masuk ke akun korban walaupun *session*nya sudah diakhiri.
<br/>
<br/>
<span class="evidence">
*The attacker can still get notification of messages that go to the victim's account even though the session has ended.*
</span>
----
## Steps to Reproduce 
* *Attacker* berhasil masuk ke akun korban.
<br/>
<span class="evidence">
The attacker has succeeded log in into the victim's account.
</span>
<br/>
<br/>
* Korban mengetahui bahawa ada orang lain yang telah masuk ke akunnya.
<br/>
<span class="evidence">
The victim knows that someone else has entered her account.
</span>
<br/>
<br/>
* Korban ingin mengakhiri session dari *attacker* dengan cara membuka pengaturan Aktifitas Login pada Pengaturan.
<br/>
<span class="evidence">
The victim wants to end the session from the attacker by opening the Login Activity setting in Settings.
</span>
<br/>
<br/>
* *Session attacker* berhasil diakhiri.
<br/>
<span class="evidence">
The attacker's session has successfully ended.
</span>
<br/>
<br/>
* Walaupun *attacker* sudah berhasil keluar dari akun korban, tetapi apabila ada pesan yang masuk ke akun korban, perangkat *attacker* masih mendapatkan notifikasi pesan yang masuk.
<br/>
<span class="evidence">
Although the attacker has logged out from the victim's account, if there is a message that enters the victim's account, the attacker's device still gets a notification of the incoming message.
</span>
<br/>
<br/>
* Mungkin hal ini terjadi karena, token FCM dari *device attacker* masih terdaftar sehingga *device attacker* masih menerima notifikasi pesan baru.
<br/>
<span class="evidence">
Maybe this happened because FCM tokens from the device attacker were still registered so that the device attacker was still receiving new message notifications.
</span>
<br/>
<p align="center">
  <img src="https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/facebook01/facebook_bounty_01.png">
</p>

----
## Timeline
* 15 Nov 2019 : Melaporkan ke Facebook.
* 19 Nov 2019 : Facebook menerima laporan dan meminta informasi lebih detil.
* 27 Jan 2020 : Facebook menyatakan laporan valid dan memberikan hadiah $750.

----
