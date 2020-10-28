# Call
*The flag format is: HTB{PASSWORD}*

Lo abrimos con audacity y podemos ver las marcas claras en las frecuencias de DTMF, usaremos esta [tool](https://github.com/ribt/dtmf-decoder) mediante wine
```
23 31 43 47 83 71 19 23 43 17 64 73 72 33 11 17 71 41 13
```
Vemos que todos menos 2 son primos, miremos si el prime cipher me da algo util hasta el primer no primo
```
I K N O W T H I N G
```
Bien, esto tiene bastante sentido , nos queda esto:
```
64 73 72 33 11 17 71 41 13
```
Hay varias opciones posible para organizar eso en numeros primos, la mas logica es esta:
```
67 37 23 31 11 7 71 41 13
```
Que se traduce en 
```
S L I K E D T M F
```
Finalmente la pass seria 
```
IKNOWTHINGSLIKEDTMF
```
Flag: **HTB{IKNOWTHINGSLIKEDTMF}**
