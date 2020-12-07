# Misc
## Vulnerable Adventure 

Nos dan el siguiente cliente para las dos task
```python
import struct
import socket
import random

s = socket.socket()
port  = 1013
s.connect(('35.238.225.156',port))


coins = 100
health = 100
mana = 100
posx = 0
posy = 0
goblin = 0
terminated = 0
dummyflag = 0
vuln = random.randint(1,500)

while(1 == 1):
	print "Welcome to game"
	print "What would you like to do?"
	print "1. Speak to goblin in front of you(2 energy) \n 2. Move Forward 1 step(1 energy) \n 3. Move Backward 1 step(1 energy) \n 4. Move Left 1 step(1 energy) \n 5. Move Right 1 step(1 energy) \n 6. Exit "

	choice = input("Enter your choice : ")
	if choice == vuln:
		dummyflag = 1
	
	if choice == 1 :
		goblin = 1
	elif choice == 2 :
		posy = 1
	elif choice == 3 :
		posy = -1
	elif choice == 4 :
		posx = -1
	elif choice == 5 :
		posx = 1
	elif choice == 6 :
		terminated = 1

	packet = struct.pack("iiiiiiii",posx,posy,coins,health,mana,goblin,terminated,dummyflag)
	#print(packet)
	s.send(packet)
	print(s.recv(500*1024))
	if(terminated == 1):
		break;
	goblin = 0
	dummyflag = 0
	posx = 0
	posy = 0
```
### Parte 1
![Task Description](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/boot2root/VulnAdventure1.png)

Cuando lanzamos el cliente, si hablamos con el hobit nos dira que la flag esta en la posición 9,9
Viendo el código para el movimiento de las celdas vemos que no podemos con el codigo tal y como esta ya que no suma o resta si no que iguala, es decir en vez de sumar 1 o restar 1 a la posicion lo que hace es igualarla
```python
	if choice == 1 :
		goblin = 1
	elif choice == 2 :
		posy = 1
	elif choice == 3 :
		posy = -1
	elif choice == 4 :
		posx = -1
	elif choice == 5 :
		posx = 1
```    
La manera más fácil para conseguir esta flag es establecer la posicion en 9 en ambos, es decir de esto:
```python
posx = 0
posy = 0
```
A esto:
```python
posx = 9
posy = 9
```
Si ejecutamos ahora el cliente con ese cambio, al hablar con el goblin nos dan la flag:
```
python2.7 Vuln1.py 
Welcome to game
What would you like to do?
1. Speak to goblin in front of you(2 energy) 
 2. Move Forward 1 step(1 energy) 
 3. Move Backward 1 step(1 energy) 
 4. Move Left 1 step(1 energy) 
 5. Move Right 1 step(1 energy) 
 6. Exit 
Enter your choice : 1
b00t2root{p4ck3t_inj3ct}
```
Flag **b00t2root{p4ck3t_inj3ct}**

### Parte 2
![Task Description](https://github.com/srbleu/Writeups-en-MarkDown/blob/master/CTFTasks/2k20/boot2root/VulnAdventure2.png)

Bien para este hay dos caminos en su resolución ambos en estas 2 lineas:
```python
dummyflag = 0
vuln = random.randint(1,500)
```
Si igualamos la dummyflag a 1, el servidor nos devolvera la flag automaticamente al interactuar:
```
Welcome to game
What would you like to do?
1. Speak to goblin in front of you(2 energy) 
 2. Move Forward 1 step(1 energy) 
 3. Move Backward 1 step(1 energy) 
 4. Move Left 1 step(1 energy) 
 5. Move Right 1 step(1 energy) 
 6. Exit 
Enter your choice : 2
b00t2root{python2_vuln}
```
Si por otro lado igualamos el vuln a algun numero fijo:
```
vuln = 7
```
Podremos pasar esta comparación sin problema:
```
if choice == vuln:
		dummyflag = 1
```
Al ejecutarlo seria tan sencillo como esto:
```
Welcome to game
What would you like to do?
1. Speak to goblin in front of you(2 energy) 
 2. Move Forward 1 step(1 energy) 
 3. Move Backward 1 step(1 energy) 
 4. Move Left 1 step(1 energy) 
 5. Move Right 1 step(1 energy) 
 6. Exit 
Enter your choice : 7
b00t2root{python2_vuln}
``` 
Flag **b00t2root{python2_vuln}**
