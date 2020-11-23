# No Place To Hide
*We found evidence of a password spray attack against the Domain Controller, and identified a suspicious RDP session. We'll provide you with our RDP logs and other files. Can you see what they were up to?*

Nos dan dos archivos:
```
bcache24.bmc
Cache0000.bin
```
Buscando en google veremos que son archivos de cache de RDP y tenemos una [tool](https://github.com/ANSSI-FR/bmc-tools) para poder sacar el mapas
```
python2.7 bmc-tools.py -s Cache0000.bin -d Cache -b
```
Y nos construye un mapa de la cache, si vemos lo que contiene veremos un cmd.exe partido , montando un poco el puzle tenemos lo siguiente
```
HTB{w47ch_y0ur_c0Nn3C71}
```
Con esto ya tendriamos nuestra flag:

Flag **HTB{w47ch_y0ur_c0Nn3C71}**
