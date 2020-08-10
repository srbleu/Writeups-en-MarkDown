# Gotta Catch'em All!

## Initial Scan 
```
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 58:14:75:69:1e:a9:59:5f:b2:3a:69:1c:6c:78:5c:27 (RSA)
|   256 23:f5:fb:e7:57:c2:a5:3e:c2:26:29:0e:74:db:37:c2 (ECDSA)
|_  256 f1:9b:b5:8a:b9:29:aa:b6:aa:a2:52:4a:6e:65:95:c5 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Can You Find Them All?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Enum

### HTTP

Lo primero es que desde la consola del navegador en / encontramos un array
```
[
  "Bulbasaur",
  "Charmander",
  "Squirtle",
  "Snorlax",
  "Zapdos",
  "Mew",
  "Charizard",
  "Grimer",
  "Metapod",
  "Magikarp"
]
```

Aparte en el fuente encontramos esto:
```
<pokemon>:<hack_the_pokemon>
<!--(Check console for extra surprise!)-->
```

Lo de pokemon:hack_the_pokemon podrían ser unas creds

Las pruebo en el SSH y estamos dentro

## Pokemon Planta

Una vez dentro veamos que tenemos

```
pokemon@root:~$ ls Desktop/
P0kEmOn.zip
pokemon@root:~$ unzip Desktop/P0kEmOn.zip
Archive:  Desktop/P0kEmOn.zip
   creating: P0kEmOn/
  inflating: P0kEmOn/grass-type.txt
pokemon@root:~$ cat grass-type.txt  
50 6f 4b 65 4d 6f 4e 7b 42 75 6c 62 61 73 61 75 72 7d
```
Parece base16 la decodificamos y tenemos el primer pokemon

## Pokemon Agua

Vamos a /var/www/html y vemos que tenemos dos  archivos
```bash
pokemon@root:/var/www/html$ ls
index.html  water-type.txt
```
Leemos el water-type.txt
```
Ecgudfxq_EcGmP{Ecgudfxq}
```

ROT 14, si 14 y listo
```
Squirtle_SqUaD{Squirtle}
```

## Privesc

ls -lAR , entre otras cosa me muestra esto
```bash
./Videos/Gotta/Catch/Them/ALL!:
total 12
-rw-r--r-- 1 pokemon root      78 Jun 22 23:14 Could_this_be_what_Im_looking_for?.cplusplus
```
Leamoslo:
```C++
# include <iostream>

int main() {
	std::cout << "ash : pikapika"
	return 0;
```

Parecen creds, probemoslas

```
User ash may run the following commands on root:
    (ALL : ALL) ALL
```
Premio gordo

## Roots Pokemon

```
cat /home/roots-pokemon.txt 
Pikachu!
```

## Fire pokemon

```
root@root:/home# find / -name fire* 
/etc/why_am_i_here?/fire-type.txt
root@root:/home# cat /etc/why_am_i_here?/fire-type.txt
UDBrM20wbntDaGFybWFuZGVyfQ==
```

Base64 -d y:
```
P0k3m0n{Charmander}
```

