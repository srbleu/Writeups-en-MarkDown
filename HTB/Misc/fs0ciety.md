# fs0ciety

####  We believe that there is an SSH Password inside password protected 'ZIP' folder. Can you crack the 'ZIP' folder and get the SSH password? 

### Extracción

Extraemos el zip que nos dan del challenge y veamos que hay dentro

```bash
$ unzip -P hackthebox fs0ciety.zip 
Archive:  fs0ciety.zip
    inflating: fsociety.zip      
```

Nos dan el zip password protected del que hablaba la descripción

### Cracking the zip

Extraemos el hash del zip
```bash
$:~/.JohnTheRipper/run/zip2john fsociety.zip > hash.txt
$:~/.JohnTheRipper/run/john hash.txt --wordlist=rockyou.txt
$:~/.JohnTheRipper/run/john --show hash.txt
    fsociety.zip/sshcreds_datacenter.txt:justdoit:sshcreds_datacenter.txt:fsociety.zip::fsociety.zip
```
Vemos que la contraseña es justdoit

### Segunda extracción

```bash
$ unzip -P justdoit fsociety.zip 
Archive:  fsociety.zip
  inflating: sshcreds_datacenter.txt
```bash

El archivo sshcreds_datacenter.txt contiene lo siguiente 
```Plaintext
*****************************************************************************************
Encrypted SSH credentials to access Blume ctOS : 

MDExMDEwMDEgMDExMDAxMTAgMDEwMTExMTEgMDExMTEwMDEgMDAxMTAwMDAgMDExMTAxMDEgMDEwMTExMTEgMDExMDAwMTEgMDEwMDAwMDAgMDExMDExMTAgMDEwMTExMTEgMDAxMDAxMDAgMDExMDExMDEgMDAxMTAwMTEgMDExMDExMDAgMDExMDExMDAgMDEwMTExMTEgMDExMTAxMTEgMDExMDEwMDAgMDEwMDAwMDAgMDExMTAxMDAgMDEwMTExMTEgMDExMTAxMDAgMDExMDEwMDAgMDAxMTAwMTEgMDEwMTExMTEgMDExMTAwMTAgMDAxMTAwMDAgMDExMDAwMTEgMDExMDEwMTEgMDEwMTExMTEgMDExMDEwMDEgMDExMTAwMTEgMDEwMTExMTEgMDExMDAwMTEgMDAxMTAwMDAgMDAxMTAwMDAgMDExMDEwMTEgMDExMDEwMDEgMDExMDExMTAgMDExMDAxMTE=

*****************************************************************************************
```

### Decodificación

Como el texto acaba en = podria ser base64 c
```bash
$ echo 'MDExMDEwMDEgMDExMDAxMTAgMDEwMTExMTEgMDExMTEwMDEgMDAxMTAwMDAgMDExMTAxMDEgMDEwMTExMTEgMDExMDAwMTEgMDEwMDAwMDAgMDExMDExMTAgMDEwMTExMTEgMDAxMDAxMDAgMDExMDExMDEgMDAxMTAwMTEgMDExMDExMDAgMDExMDExMDAgMDEwMTExMTEgMDExMTAxMTEgMDExMDEwMDAgMDEwMDAwMDAgMDExMTAxMDAgMDEwMTExMTEgMDExMTAxMDAgMDExMDEwMDAgMDAxMTAwMTEgMDEwMTExMTEgMDExMTAwMTAgMDAxMTAwMDAgMDExMDAwMTEgMDExMDEwMTEgMDEwMTExMTEgMDExMDEwMDEgMDExMTAwMTEgMDEwMTExMTEgMDExMDAwMTEgMDAxMTAwMDAgMDAxMTAwMDAgMDExMDEwMTEgMDExMDEwMDEgMDExMDExMTAgMDExMDAxMTE=' | base64 -d
01101001 01100110 01011111 01111001 00110000 01110101 01011111 01100011 01000000 01101110 01011111 00100100 01101101 00110011 01101100 01101100 01011111 01110111 01101000 01000000 01110100 01011111 01110100 01101000 00110011 01011111 01110010 00110000 01100011 01101011 01011111 01101001 01110011 01011111 01100011 00110000 00110000 01101011 01101001 01101110 01100111
```
Ya solo quedaría pasar de bin a ascii, cosa que podemos simplificar usando cyberchef
```cyberchef
https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)From_Binary('Space')
```
Y ya con esto tendriamos la flag, que en este caso no esta en el formato de la plataforma

```
if_y0u_c@n_$m3ll_wh@t_th3_r0ck_is_c00king
```
