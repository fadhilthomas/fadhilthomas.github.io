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
Log yang dihasilkan oleh Falco akan diteruskan ke Amazon CloudWatch agar terpusat dan nantinya akan memudahkan apabila ingin meneruskannya lagi ke SIEM atau membuat *alerting*.

#### IAM Permission
Untuk dapat meneruskan log ke Amazon CloudWatch dibutuhkan perizinan, maka perlu membuat IAM Policy.

`iam_role_policy.json`
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
```
Jalankan perintah berikut untuk membuat policy dengan nama `EKS-CloudWatchLogs`.
```
aws iam create-policy --policy-name EKS-CloudWatchLogs --policy-document file://iam_role_policy.json

```
Kemudian tempelkan policy `EKS-CloudWatchLogs` ke role EKS NodeGroup. Nama role dari EKS NodeGroup dapat dilihat dengan cara berikut:
* Buka halaman Amazon Elastic Kubernetes Service.
* Pilih `falco-cluster` di daftar Clusters.
* Pilih `falco-node` di Node groups pada tab Compute.
* Akan terlihat nama role di bagian Node IAM role ARN.

Jalankan perintah berikut untuk menambahkan policy `EKS-CloudWatchLogs` ke NodeGroup role.
```
aws iam attach-role-policy --role-name EKS-NODE-ROLE-NAME --policy-arn `aws iam list-policies | jq -r '.[][] | select(.PolicyName == "EKS-CloudWatchLogs") | .Arn'`
```

#### Fluent Bit Deployment
Setelah menyiapkan IAM Permission, kemudian dapat men*deploy* Fluent Bit.

`fluent-bit-namespace.yml`
```
apiVersion: v1
kind: Namespace
metadata:
  name: fluent-bit
```

`fluent-bit-configmap.yml`
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluent-bit-config
  namespace: fluent-bit
  labels:
    app.kubernetes.io/name: fluent-bit
data:
  fluent-bit.conf: |
    [SERVICE]
        Parsers_File  parsers.conf
    [INPUT]
        Name              tail
        Tag               falco.*
        Path              /var/log/containers/falco*.log
        Parser            falco
        DB                /var/log/flb_falco.db
        Mem_Buf_Limit     5MB
        Skip_Long_Lines   On
        Refresh_Interval  10
    [OUTPUT]
        Name cloudwatch
        Match falco.**
        region eu-west-1
        log_group_name falco
        log_stream_name alerts
        auto_create_group true
  parsers.conf: |
    [PARSER]
        Name        falco
        Format      json
        Time_Key    time
        Time_Format %Y-%m-%dT%H:%M:%S.%L
        Time_Keep   Off
        # Command      |  Decoder | Field | Optional Action
        # =============|==================|=================
        Decode_Field_As   json    log
```

`fluent-bit-daemonset.yml`
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
  namespace: fluent-bit
  labels:
    app.kubernetes.io/name: fluent-bit
spec:
  selector:
    matchLabels:
      name: fluent-bit
  template:
    metadata:
      labels:
        name: fluent-bit
    spec:
      serviceAccountName: fluent-bit
      containers:
      - name: aws-for-fluent-bit
        image: amazon/aws-for-fluent-bit:1.2.2
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
        - name: fluent-bit-config
          mountPath: /fluent-bit/etc/
        - name: mnt
          mountPath: /mnt
          readOnly: true
        resources:
          limits:
            memory: 500Mi
          requests:
            cpu: 500m
            memory: 100Mi
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
      - name: fluent-bit-config
        configMap:
          name: fluent-bit-config
      - name: mnt
        hostPath:
          path: /mnt
```

`fluent-bit-service-account.yml`
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluent-bit
  namespace: fluent-bit
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pod-log-reader
rules:
- apiGroups: [""]
  resources:
  - namespaces
  - pods
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pod-log-crb
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: pod-log-reader
subjects:
- kind: ServiceAccount
  name: fluent-bit
  namespace: fluent-bit
```

Siapkan semua manifest fluent-bit ke dalam satu folder `fluent-bit`. Jalankan perintah berikut untuk men*deploy* fluent-bit.
```
kubectl apply -f fluent-bit/
```

### Falco

Ada beberapa cara untuk memasang Falco, pada tulisan ini saya akan mencoba menggunakan Helm Chart. Terlebih dahulu unduh file `values.yaml` dari https://github.com/falcosecurity/charts/blob/master/falco/values.yaml. Ubah `json_output: false` menjadi `json_output: true` untuk menjadikan format output log Falco menjadi json.

Jalan perintah berikut untuk memasang Falco di kluster Kubernetes yang sudah dibuat sebelumnya.
```
helm install falco -f values.yaml falcosecurity/falco --namespace falco --create-namespace
```


## Referensi
1. https://falco.org/docs/getting-started/
2. https://eksctl.io/