# Beep ![Avatar](https://www.hackthebox.eu/storage/avatars/f02481d8d8020005f8d66115b3bfae11_thumb.png)

### Initial Scan

```
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
53/tcp open  domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Enumeration

#### DNS

La máquina tiene un servidor DNS en marcha, hagamos una consulta de zone transfer sobre bank.htb
```
dig axfr bank.htb @10.10.10.29

; <<>> DiG 9.16.1-Ubuntu <<>> axfr bank.htb @10.10.10.29
;; global options: +cmd
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
bank.htb.		604800	IN	NS	ns.bank.htb.
bank.htb.		604800	IN	A	10.10.10.29
ns.bank.htb.		604800	IN	A	10.10.10.29
www.bank.htb.		604800	IN	CNAME	bank.htb.
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
;; Query time: 52 msec
;; SERVER: 10.10.10.29#53(10.10.10.29)
;; WHEN: sáb ago 01 13:13:47 CEST 2020
;; XFR size: 6 records (messages 1, bytes 171)
```

#### HTTP

Tenemos varias URL que añadir a /etc/hosts gracias al DNS
```
chris.bank.htb
bank.htb
```
El dominio bank.htb nos redirige a bank.htb/login.php vamos a enumerar directorios 
```
/assets (Status: 301)
/inc (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
```
Dentro de uploads no podemos mirar pero dentro de inc si y tenemos varios archivos .php
