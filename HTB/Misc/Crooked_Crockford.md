# Crooked Crockford
## Some bits are missing 

```python
msg="r,,,,rr,rr,r,rr,r,,,,,rr,rr,r,r,,r,r,rr,,,,rr,,rr,rrr,,,r,,,r,,r,rr,,,r,r,,rrr,r,,,,r,,,,,rr,r,rr,r,,r,rrr,,rrr,r,,,r,,r,,rrr,r,,r,,,,rr,rr,r,,,,,rr,r,rrrr,,r,rrr,,r,rr"
msg.replace("r","1").replace(",","0")
```
Esto nos da un string en binario, si lo separamos en grupos de 7 y lo decodificamos obtenemos el siguiente string
```
C5P6RX38DXSPAVKNDNH6AWKK
```
Aplicamos un decoder de base32 crockford
```
allthosenumbers
```
Pongamoslo en el formaot: **HTB{allthosenumbers}**
