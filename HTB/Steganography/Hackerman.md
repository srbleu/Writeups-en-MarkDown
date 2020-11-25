# Hackerman
**
Usaremos stegoveritas:
```
stegoveritass hackerman.jpg
```
Esto nos enseña que hay datos al final:
```
5634275d694f8665957746c9619132f0
```
Una busqueda rápida nos devuelve que es el md5hash de almost, probemoslo como password para steghide
```
steghide extract -sf hackerman.jpg -p almost
anotó los datos extraídos e/"hackerman.txt".
```
Veamos que contiene:
```
SFRCezN2MWxfYzBycH0=
```
Si decodificamos esto en base64 veremos la flag

Flag **HTB{3v1l_c0rp}**
