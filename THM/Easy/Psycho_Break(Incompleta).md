# Psycho Break
## Initial Scan
```
21/tcp open  ftp     ProFTPD 1.3.5a
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 44:2f:fb:3b:f3:95:c3:c6:df:31:d6:e0:9e:99:92:42 (RSA)
|   256 92:24:36:91:7a:db:62:d2:b9:bb:43:eb:58:9b:50:14 (ECDSA)
|_  256 34:04:df:13:54:21:8d:37:7f:f8:0a:65:93:47:75:d0 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome To Becon Mental Hospital
```

## Enum
### HTTP
En la landin page vemos un comentario
```
	<!-- Sebastian sees a path through the darkness which leads to a room => /sadistRoom -->
```

Si vamos a esa localizacion y vemos un js, en el:
```
Key to locker Room => 532219a04ab7a02b56faafbec1a4c1ea 
```
Tambien se ve esto:
```

$(".btn-danger").click(function(e) {
	const key = prompt("Enter Key To The Locker Room ");
	if (key == "532219a04ab7a02b56faafbec1a4c1ea"){
		window.open("../lockerRoom/","_self")
	}
 ```
 Accedemos a la URL y vemos un texto encodeado
 ```
 Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv
 ```
 Si aplicamos atbash obtenemos esto
 ```
 Grant_me_access_to_the_map_please
 ```
 Encontramos dos endpoints 
 ```
 abandonedRoom/ <- Aqui va la pass del keeper
 SafeHeaven/ <- fotos 
 ```
 En el safe heaven hay varias fotos y un comentario
 ```
 <!-- I think I'm having a terrible nightmare. Search through me and find it ... -->
 ```
Tiramos de gobuster y hay un dir llamado 
```
/keeper
```
Tenemos un link a escapekeeper.php, si le damos nos dan una imagen y tenemos que encontrar el nombre del sitio, buscamos por google imagenes y vemos el sitio:
```
St Augustine Lighthouse
```
Vamos a meter la key que nos dan en la roomAbandonada y nos lleva a un endpoint con php
```
<!-- There is something called "shell" on current page maybe that'll help you to get out of here !!!-->
```
Hay un RCE
```
http://IP/abandonedRoom/be8bc662d1e36575a52da40beba38275/herecomeslara.php?shell=ls
```
Con ls .. podemos encontrar otro endpoint
```
680e89809965ec41e64dc7e447f175ab
```
Allí tenemos un zip, dentro de este una foto, si usamos binwalk en ella obtenemos otro jpg y un waw

En el waw tenemos codigo morse que dice: **SHOWME**
En el jpg hay algo escondido con steghide, si usamos la pass SHOWME lo extraemos 
```
	   --------------------------------------------
        //						\\
	||	(NOTE) FTP Details			||
	||      ==================			||
	||						||
	||	USER : joseph				||
	||	PASSWORD : intotheterror445		||
	||						||
	\\						//
	   --------------------------------------------
```

### FTP
Nos logueamos con estas creds en el FTP
```
-rwxr-xr-x   1 joseph   joseph   11641688 Aug 13 15:12 program
-rw-r--r--   1 joseph   joseph        974 Aug 13 15:20 random.dic
```
Obtenemos un programa que checkea por una pass y un diccionario hmmmm 1+1

Hacemos un pequeño script en python
```
import os


f = open("random.dic", "r")

for i in f:
	cmd = "./program " + i + ">> flag.txt"
	os.system(cmd)
```
Y nos da que la pass es **kidman**
```
kidman => Correct

Well Done !!!
Decode This => 55 444 3 6 2 66 7777 7 2 7777 7777 9 666 777 3 444 7777 7777 666 7777 8 777 2 66 4 33
```
Si decodeamos el multitap tenemos esta pass:
```
KIDMANSPASSWORDISSOSTRANGE
```
Con kidman:KIDMANSPASSWORDISSOSTRANGE podemos logearnos por SSH
## Privesc
Desde kidman podemos acceder al crontab y ver un cronjob
```
*/2 * * * * root python3 /var/.the_eye_of_ruvik.py
```
Tenemos permisos de escritura
```
-rwxr-xrw- 1 root root 300 Aug 14 22:43 /var/.the_eye_of_ruvik.py
```
Podemos añadir y ejecutar codigo arbitrario como root gracias a esto
