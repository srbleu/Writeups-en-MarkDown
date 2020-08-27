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
