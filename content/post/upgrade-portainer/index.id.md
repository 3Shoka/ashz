---
title: "Upgrade Portainer"
date: 2022-07-06T19:24:35+07:00
description: Upgrade portainer dari versi 2.13.1 ke 2.14
categories: ["server"]
tags: ["docker", "container", "portainer"]
---

Upgrade portainer dari versi 2.13.1 ke 2.14

## Proses backup setting
Sebelum melakukan ugrade alangkah baiknya melakukan proses backup terlebih dahulu, agar jika terjadi kesalahan kita dapat melakukan restore ke setting sebelumnya.

Untuk melakukan backup lakukan perintah berikut:
- login sebagai admin
- masuk ke menu Setting > Backup portainer
- hasil backup dapat diproteksi menggunakan enkripsi password, jika memilih password protect
- klik download backup

## Proses upgrade portainer

```bash
# stop container portainer
docker stop portainer

# hapus container portainer
docker rm portainer	

# pull image portainer versi terbaru
docker pull portainer/portainer-ce:latest 

# deploy portainer versi terbaru
docker run -d -p 8000:8000 -p 9443:9443 \
    --name=portainer --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    portainer/portainer-ce:latest

```

## Selesai
Upgrade selesai, test portainer versi terbaru di browser https://IP-ADDRESS:9443/
