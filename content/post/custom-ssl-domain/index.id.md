---
title: "Custom SSL Domain"
date: 2023-02-26T00:27:42Z
description: "Membuat custom SSL untuk domain lokal - tanpa warning dari browser"
categories: ["server"]
tags: ["ssl","domain"]
---

Cara membuat custom sertifikat ssl untuk domain lokal sehingga bisa mengakses domain lokal dengan https tanpa ada warning dari browser. 
Sertifikat yang akan dibuat adalah untuk domain dan IP berikut: 
 - Domain: ashok.lan (domain untuk jaringan lokal)
 - IP: 10.0.11.240 (IP lokal juga)

## Generate Certificate Authority (CA)

Certificate Authority (CA) adalah organisasi yang melakukan verifikasi terhadap sertifikat digital.

- Generate RSA key

`openssl genrsa -aes256 -out ca-key.pem 4096` 
masukkan pass phrase: `xxx`, akan menghasilkan file `ca-key.pem`

- generate public CA cert

`openssl req -new -x509 -sha256 -days 365 -key ca-key.pem -out ca.pem`

``` bash
# output command
Enter pass phrase for ca-key.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:ID
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Ashz Inc.
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:Ashz
Email Address []:xxx@ashz.xyz
```
akan menghasilkan file `ca.pem`

- opsional, melihat konten sertifikat 

`openssl x509 -in ca.pem -text`

## Generate sertifikat

- membuat rsa key

`openssl genrsa -out cert-key.pem 4096`
akan menghasilkan file `cert-key.pem`

- membuat Sertificate Signing Request (CSR)

`openssl req -new -sha256 -key cert-key.pem -out cert.csr`

``` bash
# output command
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:ID
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Ashz Inc.
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:Ashok
Email Address []:xxx@ashok.lan

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```

menghasilkan file `cert.csr`

- membuat file untuk altName

disini saya membuat domain wildcard untuk *.ashok.lan dan menambahkan IP, agar IP dan semua subdomain tersebut dapat diakses menggunakan https tanpa ada warning dari browser. 

`echo "subjectAltName=DNS:*.ashok.lan,IP:10.0.11.244" > extfile.cnf`

menghasilkan file `extfile.cnf`

- membuat sertifikat

`openssl x509 -req -sha256 -days 365 -in cert.csr -CA ca.pem -CAkey ca-key.pem -out cert.pem -extfile extfile.cnf -CAcreateserial`

``` bash
# output command
Certificate request self-signature ok
subject=C = ID, ST = Some-State, O = Ashz Inc., CN = Ashok, emailAddress = xxx@ashok.lan
Enter pass phrase for ca-key.pem:
```
masukkan pass phrase CA, kemudian enter
menghasilkan file `cert.pem`

Selanjutnya install `ca.pem` pada perangkat yang akan mengakses domain *.ashok.lan dan gunakan file `cert-key.pem` dan `cert.pem` untuk setting ssl domain dan subdomain ashok.lan

sumber refrensi: 
 - [ChristianLempa](https://github.com/ChristianLempa/cheat-sheets/blob/main/misc/ssl-certs.md)