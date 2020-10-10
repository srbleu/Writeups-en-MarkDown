# Old is gold

*Old algorithms are not a waste, but are really precious...*

Nos dan un pdf password protected, saquemos el hash del mismo usando jtr
```
pdf2john.pl 0ld\ is\ g0ld.pdf > hash.txt
```
Ahora crackeemos el hash 
```
john hash.txt --wordlist=rockyou.txt
jumanji69        (0ld is g0ld.pdf)
```
En el pdf hay una foto de samuel morse y un fragmento de codigo morse
```
.-. .---- .--. ... .- -- ..- ...-- .-.. -- ----- .-. ... ...--
```
Si lo decodificamos nos queda el siguiente texto:
```
R1PSAMU3LM0RS3
```
Flag: **R1PSAMU3LM0RS3**
