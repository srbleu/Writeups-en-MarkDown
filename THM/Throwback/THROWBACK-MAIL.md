# MAIL

## Initial Scan
```
22/tcp  open  ssh      OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a8:d6:de:a5:9f:ca:5f:e5:65:c2:33:e4:bb:a1:1e:2d (RSA)
|   256 2f:5f:54:d2:60:90:cd:62:cf:52:d2:97:8f:4c:fd:5b (ECDSA)
|_  256 73:c0:b9:db:9f:0c:70:af:36:66:c2:d5:81:80:ed:b8 (ED25519)
80/tcp  open  http     Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-title: Throwback Hacks - Login
|_Requested resource was src/login.php
143/tcp open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: LOGINDISABLEDA0001 capabilities STARTTLS have OK LOGIN-REFERRALS post-login SASL-IR IDLE ENABLE Pre-login ID more listed LITERAL+ IMAP4rev1
| ssl-cert: Subject: commonName=ip-10-40-119-232.eu-west-1.compute.internal
| Subject Alternative Name: DNS:ip-10-40-119-232.eu-west-1.compute.internal
| Not valid before: 2020-07-25T15:51:57
|_Not valid after:  2030-07-23T15:51:57
|_ssl-date: TLS randomness does not represent time
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
|_imap-capabilities: capabilities SASL-IR have OK LOGIN-REFERRALS post-login AUTH=PLAINA0001 IDLE ENABLE Pre-login ID more listed LITERAL+ IMAP4rev1
| ssl-cert: Subject: commonName=ip-10-40-119-232.eu-west-1.compute.internal
| Subject Alternative Name: DNS:ip-10-40-119-232.eu-west-1.compute.internal
| Not valid before: 2020-07-25T15:51:57
|_Not valid after:  2030-07-23T15:51:57
|_ssl-date: TLS randomness does not represent time
```
## Enum
### Web Server
El web server nos da unas low level credentials para una guest account
```
tbhguest:WelcomeTBH1!
```
Con estas creds aparte de dos flags en esta maquina podemos enumerar una serie de correos de este servidor
```
HumphreyW@throwback.local
SummersW@throwback.local
FoxxR@throwback.local
DaibaN@throwback.local
PeanutbutterM@throwback.local
PetersJ@throwback.local
DaviesJ@throwback.local
BlaireJ@throwback.local
GongoH@throwback.local
MurphyF@throwback.local
JeffersD@throwback.local
HorsemanB@throwback.local
```
Esto puede ser util luego
