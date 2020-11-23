# Persistence
*We're noticing some strange connections from a critical PC that can't be replaced. We've run an AV scan to delete the malicious files and rebooted the box, but the connections get re-established. We've taken a backup of some critical system files, can you help us figure out what's going on?*

Bien lo primero es saber que tipo de artefacto tenemos delante, para ello usaremos el comando file
```
file query 
query: MS Windows registry file, NT/2000 or above
```
Bien dado que es un fichero de registros usaremos la herramienta **regdump.pl** de libparse (desde linux) 
Con la información que tenemos parece que se ha añadigo algún tipo de orden maliciosa en el arranque, por ello deberiamos consultar los registros ligados al arranque
```
regdump.pl query 'Software\Microsoft\Windows\CurrentVersion\Run' -v
ROOT\Software\Microsoft\Windows\CurrentVersion\Run [2020-10-27T04:38:55Z]
Windows Update (REG_SZ) = C:\Windows\System32\SFRCezFfQzRuX2t3M3J5XzRMUjE5aDd9.exe
```
Si decodificamos el nombre del exe tendremos la flag:
```
echo 'SFRCezFfQzRuX2t3M3J5XzRMUjE5aDd9' | base64 -d
HTB{1_C4n_kw3ry_4LR19h7}
```
Flag: **HTB{1_C4n_kw3ry_4LR19h7}**
