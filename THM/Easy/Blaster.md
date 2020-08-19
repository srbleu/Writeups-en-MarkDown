# Blaster
Máquina windows sencillita, con un privesc curioso

Interesantes: CVE-2019-1388
## Initial Scan
```
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2020-08-14T14:11:41+00:00
```
## Enum
### HTTP
#### Gobusting
```
/retro (Status: 301)
```
Accedemos a este directorio y encontramos un posible usuario, Wade , si checkeamos los post de este usuario encontraremos tambien una contraseña, con estas dos cosas el acceso esta practicamente listo
```
wade:parzival
```
Accedemos a la máquina usando un cliente de RDP, en mi caso Remmina

## Privesc
Grupos de nuestro usuario
```
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group                       
BUILTIN\Remote Desktop Users           Alias            S-1-5-32-555 Mandatory group, Enabled by default, Enabled group                       
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\REMOTE INTERACTIVE LOGON  Well-known group S-1-5-14     Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group                       
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group                       
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group                       
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192   
```
Parece que ya tenemos el hhupd dentro del sistema, este archivo es el necesario para explotar el CVE-2019-1388 
```
https://www.youtube.com/watch?v=3BQKpPNlTSo
```
# Analisís de la intrusión
### Gestión de contraseñas 
Definivamente posterar la contraseña del administrador en texto plano en un comentario de la web fue una terrible idea 
### CVE-2019-1388
La máquina objetivo era vulnerable a dicho CVE lo que nos permitio elevar privilegios de manera local, ya que no esta parcheado

# Soluciones
### CVE-2019-1388
El sistema operativo no esta actualizado, simplemente actualizando a una versión más reciente esta explotación debería ser mitigada
