# Ignite
## Initial Scan
```
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS

```
## Enum 
### Robots.txt
/fuel esta deshabilitado, entremos a ver que hay 
### Fuel
Tenenemos un FUEL CMS corriendo, las creds por defecto estan todavia activas
```
admin:admin
```
Si buscamos dentro del CMS encontraremos un enlace a la documentacion de la version que usamos,asi podemos ver que es la 1.4

## Exploit
Para nuestra versión tenemos el siguiente exploit
```
fuelCMS 1.4.1 - Remote Code Execution                                                                            | linux/webapps/47138.py
```
Modificamos el script cambiando la url, abrimos BurpSuite y lanzamos el exploit, una vez lanzado subimos una reverse shell y accedemos a esta desde el navegador

## Privesc
Enumerando localmente encontramos un archivo con una base de datos
```
/var/www/html/fuel/application/config/database.php
```
Podemos ver las credenciales en texto plano para el root en ese archivo
```
	'username' => 'root',
	'password' => 'mememe',
```

# Analisís de la intrusión
## Default creds
En fuel CMS se han mantenido las credenciales por defecto lo que nos permitio acceder al sistema
## CVE-2018-16763 
La version del CVE utilizada es vulnerable a dicho CVE, lo que nos permitio obtener un RCE en la máquina objetivo
## No hashing passwords
Las credenciales del root fueron encontradas en texto plano en el sistema, lo que nos permitio elevar privilegios en el sistema
