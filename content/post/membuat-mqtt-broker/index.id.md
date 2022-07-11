---
title: "Membuat MQTT Broker"
date: 2022-07-11T11:27:34+07:00
description: "Membuat MQTT Broker menggunakan docker dan portainer"
categories: ["server"]
tags: ["container", "docker", "mqtt", "mosquitto", "portainer"]
---

Kali ini saya akan membuat mqtt broker menggunakan Eclipse Mosquitto, selain gratis, setup nya juga gampang (setidaknya untuk kebutuhan sendiri). Mosquitto akan dijalankan di dalam container docker menggunakan aplikasi portainer. Portainer adalah aplikasi GUI berbasis web yang memudahkan untuk manajemen docker. 

## Persiapan file dan direktori

Container Mosquitto membutuhkan 3 folder untuk menyimpan konfigurasi, data dan log

```bash
# perintah untuk membuat 3 folder
mkdir -p mosquitto/{config,data,log} 
```

Kemudian membuat file konfigurasi awal mosquitto

```bash
# membuat file mosquitto.conf
vi mosquitto/config/mosquitto.conf
```

isi file konfigurasi
```conf
# file mosquitto.conf
allow_anonymous true # untuk konek ke broker tanpa menggunakan user & password
persistence true
persistence_location /mosquitto/data
# simpan log ke dalam file
log_dest file /mosquitto/log/mosquitto.log 
# menampilkan log ke stdout docker
# log_dest stdout 

listener 1883 # default port 
```

## Konfigurasi di portainer
Untuk membuat container di portainer, pilih menu Containers > Add container. Di halaman Create container isikan parameter berikut:

- **Name**: mosquitto
- **Image**: eclipse-mosquitto
- **Manual network port publishing**:
	- **host**: 1883 > **container**: 1883, port untuk **host** bisa menggunakan port acak, tidak harus sama 1883
- **Command & logging**:
    - **User**: 1883, untuk user **mosquitto** di dalam container
    - **Console**: Interactive & TTY
- **Volumes**: container > host
    - /mosquitto/config/mosquitto.conf > /home/orangepi/mosquitto/config/mosquitto.conf
    - /mosquitto/data > /home/orangepi/mosquitto/data
    - /mosquitto/log > /home/orangepi/mosquitto/log
- **Restart policy**: Always

Setelah semua diisi, klik **Deploy the container**. Setelah container sukses dibuat dan status running, Saatnya testing.

## Test koneksi broker

Testing broker mqtt menggunakan aplikasi MQTT Explorer, isi parameter berikut:
**Nama**: mqtt broker, *isi nama untuk koneksi*
**Protocol**: mqtt://
**Host**: 10.0.11.240, *alamat IP broker*
**Port**: 1883, *port listener mosquitto, sesuaikan dengan setting port host*
Username dan password, sementara dikosongkan
Klik **Save** dan **Connect**

Jika koneksi berhasil, akan muncul tampilan explore mqtt broker, dan di log mosquitto mucul log baru :
```log
1657180721: New client connected from 10.0.11.240:56026 as mqtt-explorer-0261386d (p2, c1, k60).
```

Untuk cek log, jalankan perintah `sudo tail -f mosquitto/log/mosquitto.log`. Jika file konfigurasi *log_dest* di set *stdout*, untuk cek log `docker logs -f mosquitto`

## Menambahkan user untuk akses broker
Sangat penting untuk setting akun untuk otentikasi, agar tidak sembarang pengguna bisa mengakses mqtt broker.

### Membuat password file
Untuk membuat password file gunakan perintah `mosquito_passwd`, perintah lengkapnya sebagai berikut `mosquitto_passwd -c <password file> <username>`, parameter `-c` untuk membuat file baru. Karena saya membuat mosquitto di dalam docker container, jadi untuk menjalankan `mosquitto_passwd` harus diawali dengan perintah *docker exec*, `docker exec -it <nama container> <perintah>`

Perintah lengkap :
```bash
# eksekusi perintah untuk membuat user mosquitto
docker exec -it mosquitto mosquitto_passwd -c /mosquitto/config/passwd ashoka
```
Setelah menjalankan perintah diatas maka akan muncul prompt untuk memasukkan password, ketik password kemudian enter dan ulangi password untuk konfirmasi.

Untuk cek file hasil dari perintah diatas, jalankan perintah
```bash
# melihat isi file passwd
docker exec -it mosquitto cat /mosquitto/config/passwd
```
File akan berisi username dan password yang telah dienkripsi.

### Update file config

Untuk menerapkan otentikasi pada broker, harus ditambahkan konfigurasi untuk memuat file password yang sudah dibuat tadi
```conf
#file: mosquitto/config/mosquitto.conf
allow_anonymous true # biarkan anonim tetap true dulu
persistence true
persistence_location /mosquitto/data
log_dest file /mosquitto/log/mosquitto.log

listener 1883
password_file /mosquitto/config/passwd # baris ini yang ditambahkan
```
Setelah update file config, restart container mosquitto, kemudian test menggunakan MQTT explorer
jika sudah berhasil test login mqtt explorer dengan username dan password yang baru dibuat, 
maka parameter file konfigurasi `allow_anonymous true` perlu diubah menjadi `allow_anonymous false` agar tidak sembarang orang bisa mengakses broker mosquitto.

