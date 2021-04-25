# Crypto
## Phasestream 1

```
The aliens are trying to build a secure cipher to encrypt all our games called "PhaseStream". They've heard that stream ciphers are pretty good. The aliens have learned of the XOR operation which is used to encrypt a plaintext with a key. They believe that XOR using a repeated 5-byte key is enough to build a strong stream cipher. Such silly aliens! Here's a flag they encrypted this way earlier. Can you decrypt it (hint: what's the flag format?) 2e313f2702184c5a0b1e321205550e03261b094d5c171f56011904
```

Bien, sabemos que han cifrado la clave mediante xor con 5 bytes, y conocemos el formato de la flag CHTB{...}, con esto podemos solucionar el reto sin más problema.

Al ser xor un cifrado simetrico, saber la longitud de la clave y saber los 5 primeros caracteres de la flag gracias al formato, podemos obtener la clave aplicando un xor

![](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k21/Cyber%20Apocalypse%202021/Images/Captura%20de%20pantalla%202021-04-25%20a%20las%2022.50.56.png)

Una vez que tenemos nuestra clave, sacar la flag es casi inmediato

![](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k21/Cyber%20Apocalypse%202021/Images/Captura%20de%20pantalla%202021-04-25%20a%20las%2022.52.08.png)

```
FLAG: CHTB{u51ng_kn0wn_pl41nt3xt}
```

## Phasestream 2

```
The aliens have learned of a new concept called "security by obscurity". Fortunately for us they think it is a great idea and not a description of a common mistake. We've intercepted some alien comms and think they are XORing flags with a single-byte key and hiding the result inside 9999 lines of random data, Can you find the flag?
```

Bien, siendo una clave de 1 byte es facilmente atacable mediante fuerza bruta, el problema son las 10000 lineas, facil de atacar

![](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k21/Cyber%20Apocalypse%202021/Images/Captura%20de%20pantalla%202021-04-25%20a%20las%2023.14.09.png)

```
FLAG: CHTB{n33dl3_1n_4_h4yst4ck}
```

## Phasestream 3

```
The aliens have learned the stupidity of their misunderstanding of Kerckhoffs's principle. Now they're going to use a well-known stream cipher (AES in CTR mode) with a strong key. And they'll happily give us poor humans the source because they're so confident it's secure!
```

La idea del reto es sencilla, la reutilización de claves y AES-CTR son una mala idea usando el mismo contador, sobre todo sabiendo un plaintex con su correspondiente ciphertext
```python
from Crypto.Cipher import AES
from Crypto.Util import Counter
import os

KEY = os.urandom(16)


def encrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_CTR, counter=Counter.new(128))
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext.hex()


test = b"No right of private conversation was enumerated in the Constitution. I don't suppose it occurred to anyone at the time that it could be prevented."
print(encrypt(test))

with open('flag.txt', 'rb') as f:
    flag = f.read().strip()
print(encrypt(flag))

```
Averiguemos el xor que se ha aplicado

![](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k21/Cyber%20Apocalypse%202021/Images/Captura%20de%20pantalla%202021-04-25%20a%20las%2023.24.27.png)

Al estar usandose misma clave y counter podemos sencillamente aplicar el mixmo xor al string de la flag

![](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k21/Cyber%20Apocalypse%202021/Images/Captura%20de%20pantalla%202021-04-25%20a%20las%2023.27.17.png)

```
FLAG: CHTB{r3u53d_k3Y_4TT4cK}
```

## Phasestream 4

```
The aliens saw us break PhaseStream 3 and have proposed a quick fix to protect their new cipher.
```

Este reto es muy parecido al anterior pero con cantidades industriales de guessing.
