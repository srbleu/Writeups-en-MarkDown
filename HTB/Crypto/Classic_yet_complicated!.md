# Classic, yet complicated!
*Find the plaintext, the key is your flag! Flag format : HTB{key in lowercase}*
Nos dan el siguiente ciphertext:
```
alp gwcsepul gtavaf, nlv prgpbpsu mb h jcpbyvdlq, ipltga rv glniypfa we ekl 16xs nsjhlcb. px td o lccjdstslpahzn fptspf xstlxzi te iosj ezv sc xcns ttsoic lzlvrmhaw ez sjqijsa xsp rwhr. tq vxspf sciov, alp wsphvcv pr ess rwxpqlvp nwlvvc dyi dswbhvo ef htqtafvyw hqzfbpg, ezutewwm zcep xzmyr o scio ry tscoos rd woi pyqnmgelvr vpm . qbctnl xsp akbflowllmspwt nlwlpcg, lccjdstslpahzn fptspfo oip qvx dfgysgelipp ec bfvbxlrnj ojocjvpw, ld akfv ekhr zys hskehy my eva dclluxpih yoe mh yiacsoseehk fj l gebxwh sieesn we ekl iynfudktru. xsp yam zd woi qwoc.
```
Buscamos un cifrado clasico con clave, vigenere parece un buen candidato, usaremos este [solver](https://www.dcode.fr/cifrado-vigenere), si aplicamos el bruteforce:
```
the vigenere cipher, was invented by a frenchman, blaise de vigenere in the 16th century. it is a polyalphabetic cipher because it uses two or more cipher alphabets to encrypt the data. in other words, the letters in the vigenere cipher are shifted by different amounts, normally done using a word or phrase as the encryption key . unlike the monoalphabetic ciphers, polyalphabetic ciphers are not susceptible to frequency analysis, as more than one letter in the plaintext can be represented by a single letter in the encryption. the key is the flag.
```
La key es helloworld

Flag **HTB{helloworld}**
