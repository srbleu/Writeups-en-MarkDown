# Anthem
Room bastante sencillota :v

## Initial Scan
```
80/tcp    open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-LU09299160F
|   NetBIOS_Domain_Name: WIN-LU09299160F
|   NetBIOS_Computer_Name: WIN-LU09299160F
|   DNS_Domain_Name: WIN-LU09299160F
|   DNS_Computer_Name: WIN-LU09299160F
|   Product_Version: 10.0.17763
|_  System_Time: 2020-08-14T15:57:59+00:00
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Not valid before: 2020-04-04T22:56:38
|_Not valid after:  2020-10-04T22:56:38
|_ssl-date: 2020-08-14T15:58:10+00:00; 0s from scanner time.
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
```
## Enum
### HTTP 
En robots.txt tenmos un string fuera de lo común
```
UmbracoIsTheBest!
```
Podria ser una pass solo necesitariamos un user, para acceder entonces ,sigamos enumerando

Buscando en las entradas tenemos un correo perteneciente a Jane Doe
```
JD@anthem.com 
```
Y una entrada con un poema sobre una administrador, el poema es sobre Solomon Grundy, si lo ponemos en el mismo formato que el anterior tendriamos el siguiente correo
```
SG@anthem.com 
```
Vamos a probar en el CMS las creds y vemos que finciona, tras un rato intententando saltar cai en que teniamos un RDP probamos las mismas creds y entramos

## Privesc

En C:\ hay una carpeta oculta llamada backup que contiene un archivo llamado restore, tenemos permisos para cambiar permisos pero no para leerlo, si cambiamos los permisos y nos damos permiso de Lectura tendremos la pass del admi

Ya solo quedaria autenticarnos para leer la flag

# Analisis de la intrusión
## Information leak
Poner las contraseñas en un lugar como robots.txt aunque no diga de manera explicita que es una contraseña es evidentemente una mala práctica que debe ser evitada.
## Reutilización de contraseñas
Utilizar la misma contraseña para el CMS que para el usuario en la máquina conlleva que si se compromete un servicio al atacante le sea mas sencillo comprometer la máquina entera
## Backup
La existencia de un backup de la contraseña del administrador en una carpeta perteneciente a un usuario sin privilegios conlleva que en caso de comprometer la cuenta de este usuario , facilite el compromiso de la máquina entera
