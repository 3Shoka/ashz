---
title: "Setup Blog Ini"
date: 2023-01-21T01:40:27Z
description: "Setup awal blog ini di perangkat baru"
categories: ["catatan"]
tags: ["hugo","blog"]
---

Setup ini biasanya dilakukan setiap setelah install ulang pc, ganti pc atau mau update blog di perangkat baru yang belum ada skrip blog ini.

## Clone repo

Jalankan perintah
`git clone git@github.com:3Shoka/ashz.git`

Pindah ke direktori
`cd ashz`

## Install hugo 
Sumber refrensi: https://gohugo.io/installation/linux/

Jalankan perintah `sudo apt install hugo`

Cek versi hugo 
`hugo version`
```bash
# respon dari cek versi hugo
hugo v0.92.2+extended linux/arm64 BuildDate=2022-02-23T16:47:50Z VendorInfo=ubuntu:0.92.2-1
```

## Update submodule
Perintah ini digunakan untuk clone theme Stack yang ditambahkan menggunakan git submodule

`git submodule update --init`

## Jalankan server
`hugo server`

Setup selesai, selanjutnya tinggal update blog dengan menambah halaman atau postingan.

