# Bank Heist

*You get to the scene of a bank heist and find that you have caught one person. Under further analysis of the persons flip phone you see a message that seems suspicious. Can you figure out what the message to put this guy in jail?*

El archivo que nos dan contiene ele siguiente texto
```
444333 99966688 277733 7773323444664 84433 22244474433777, 99966688 277733 666552999. 99966688777 777744277733 666333 84433 443344477778 4447777 44466 99966688777 4466688777733. 84433 5533999 8666 84433 55566622255 4447777 22335556669. 4666 8666 727774447777.

47777888 995559888 4555 47777888 44999988 666555997 : 8555444888477744488866888648833369!!
```
Por la forma de escribir los numeros con signos de puntuación de por medio y tal podriía ser lo conocido como multitap cipher, usaremos un [decoder online](https://www.dcode.fr/multitap-abc-cipher) para facilitarnos el trabajo:

```
IIGF YOU ARE READING THE CIPHER YOU ARE OKAY 
YOUR SHARE OF THE HEIST IS IN YOUR HOUSE 
THE KEY TO THE LOCK IS BELOW GO TO PARIS 

GSV XLWV GL GSV HZU OLXP TLIVGRIVNVMGUFMW 
```
Esto ultimo es bastante extraño, con un poco de experiencia sabremo que GSV = THE en Atbash cipher, si lo [decodificamos](http://rumkin.com/tools/cipher/atbash.php):
```
THE CODE TO THE SAF LOCK GORETIREMENTFUND 
```
Flag: **HTB{GORETIREMENTFUND}**