---
title: "Cloud-Native Runtime Security dengan Falco di Amazon EKS"
date: '2022-08-13 00:00:00'
tags: ['falco','cloud-native','explore']
categories: ['exploration','cloud-native']
author: "fadhilthomas"
draft: false
---

![alt text](/falco01/falco-logo.webp)

Tulisan ini dibuat sebagai catatan saat mencoba Falco untuk memantau *container* secara *runtime* di Amazon EKS. 

## Apa itu Falco?
Falco adalah aplikasi *runtime security* yang berlisensi *open-source* dan gratis dikembangkan Sysdic, Inc. Saat tulisan ini dibuat, Falco masuk ke dalam CNCF project dengan status inkubasi.

## Apa manfaat menggunakan Falco?

Falco dapat mendeteksi dan mengirimkan notifikasi apabila ada aktifitas di dalam *container* yang dianggap berbahaya sesuai dengan aturan atau *rule* yang sudah dibuat sebelumnya. Falco dapat memantau system call Linux dengan menggunakan kernel module atau eBPF probe dari kernel secara *runtime*.

Falco memiliki beberapa *rule* bawaan, diantaranya:
* *Privilege escalation* menggunakan *privileged containers*
* Perubahan namespace menggunakan aplikasi seperti `setns`
* Aktivitas baca atau tulis ke direktori terkenal seperti `/etc`, `/usr/bin`, `/usr/sbin`, dan lain-lain
* Pembuatan `symlinks`
* Perubahan `ownership` dan `mode`
* Koneksi jaringan yang tidak terduga atau perubahan *socket*
* Memunculkan *process* menggunakan `execve`
* Mengeksekusi *shell binaries* seperti `sh`, `bash`, `csh`, `zsh`, dan lain-lain
* Mengeksekusi SSH *binaries* seperti `ssh`, `scp`, `sftp`, dan lain-lain
* Perubahan Linux `coreutils` *executables*
* Perubahan login *binaries*
* Perubahan `shadowutil` or `passwd` *executables* seperti `shadowconfig`, `pwck`, `chpasswd`, `getpasswd`, `change`, `useradd`, dan lain-lain.

Penjelasan singkat untuk Falco sudah cukup, untuk ingin tahu lebih detil bisa lihat dokumentasi Falco di https://falco.org/docs/ dan daftar lengkap *rule* bisa lihat di https://github.com/falcosecurity/falco/tree/master/rules.

## Bagaimana memasang Falco?

Berikut adalah gambaran umum arsitektur yang akan di*deploy* pada tulisan ini.
![alt text](/falco01/falco-architecture.png)

### Kubernetes Cluster
Sebelum dapat memasang Falco, terlebih dahulu men*deploy* sebuah kluster Kubernetes. Di tulisan ini, saya memilih Amazon EKS dengan bantuan aplikasi `eksctl` dengan CloudFormation. Berikut adalah manifest yang digunakan untuk men*deploy* Kubernetes kluster.

`falco-cluster.yml`
```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: falco-cluster
  region: ap-southeast-1
  version: "1.19"

managedNodeGroups:
  - name: falco-node
    minSize: 1
    maxSize: 2
    instanceType: t3a.small
    amiFamily: AmazonLinux2
    volumeSize: 30
    volumeEncrypted: true
    desiredCapacity: 1
```

Jalankan perintah berikut untuk membuat kluster Kubernetes.
```
eksctl create cluster -f falco-cluster.yml
```

Setelah proses pembuatan kluster berhasil, maka akan tersimpan file kube config di path `~/.kube/config`

### Log Forwading


### Falco

Ada beberapa cara untuk memasang Falco, pada tulisan ini saya akan mencoba menggunakan Helm Chart. Terlebih dahulu unduh file `values.yaml` dari https://github.com/falcosecurity/charts/blob/master/falco/values.yaml. Ubah `json_output: false` menjadi `json_output: true` untuk menjadikan format output log Falco menjadi json.

Jalan perintah berikut untuk memasang Falco di kluster Kubernetes yang sudah dibuat sebelumnya.



## Referensi
1. https://falco.org/docs/getting-started/
2. https://eksctl.io/