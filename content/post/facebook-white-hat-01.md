---
title: "[Writeup][Bug Bounty][Instagram] Instagram Still Send New DMs and Video Calls to Device After Logout [ID][EN]"
date: '2020-04-16 11:00:00'
tags: ['bugbounty','instagram','writeup','facebook']
categories: ['writeup']
author: "fadhilthomas"
draft: false
---

![alt text](/facebook01/instagram_logo.png)

Setelah pengguna melakukan *logout* akunnya, *session* seharusnya di *invalidate* untuk menghentikan aksi yang membutuhkan otentikasi seperti menerima notifikasi pesan yang masuk. Pada postingan ini membahas tentang Instagram yang menampilkan notifikasi pesan masuk walaupun pengguna telah mengakhiri *session* akunnya.
>*After the user logs out of his account, the session should be invalidated to stop actions that require authentication such as receiving notifications of incoming messages. This post is about Instagram, which shows incoming message notifications even though the user has ended his account session.*

----
## Impact
*Attacker* masih bisa mendapatkan notifikasi pesan yang masuk ke akun korban walaupun *session*nya sudah diakhiri.
>*The attacker can still get notification of messages that go to the victim's account even though the session has ended.*

----
## Steps to Reproduce 
* *Attacker* berhasil masuk ke akun korban.
>*The attacker has succeeded log in into the victim's account.*
* Korban mengetahui bahawa ada orang lain yang telah masuk ke akunnya.
>*The victim knows that someone else has entered her account.*
* Korban ingin mengakhiri session dari *attacker* dengan cara membuka pengaturan Aktifitas Login pada Pengaturan.
>*The victim wants to end the session from the attacker by opening the Login Activity setting in Settings.*
* *Session attacker* berhasil diakhiri.
>*The attacker's session has successfully ended.*
* Walaupun *attacker* sudah berhasil keluar dari akun korban, tetapi apabila ada pesan yang masuk ke akun korban, perangkat *attacker* masih mendapatkan notifikasi pesan yang masuk.
>*Although the attacker has logged out from the victim's account, if there is a message that enters the victim's account, the attacker's device still gets a notification of the incoming message.*
* Mungkin hal ini terjadi karena, token FCM dari *device attacker* masih terdaftar sehingga *device attacker* masih menerima notifikasi pesan baru.
>*Maybe this happened because FCM tokens from the device attacker were still registered so that the device attacker was still receiving new message notifications.*

![alt text](/facebook01/facebook_bounty_01.png)

----
## Timeline
* 15 Nov 2019 : Melaporkan ke Facebook.
* 19 Nov 2019 : Facebook menerima laporan dan meminta informasi lebih detil.
* 27 Jan 2020 : Facebook menyatakan laporan valid dan memberikan hadiah $750.