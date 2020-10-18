# Hacktober
Aquí estan mis writeups para el Hacktober CTF donde mi equipo y yo quedamos en el puesto 183 de 1062 participantes 
## Start
### Rules 1
*Submit this as the flag you've read and understand the rules: flag{pl4y_by_th3_ru13s}*

Flag: **flag{pl4y_by_th3_ru13s}**
### DEADFACE 1
How many threat actors are identified on the DEADFACE intel page? Submit the flag as flag{#}.
*En la pagina de intel hay 7 miembros*

Flag: **flag{7}**

### DEADFACE 2
*According to the intel on DEADFACE, what is the username of the threat actor believed to be the second-in-command of DEADFACE? Submit the flag as flag{username}.*

Vamos a la pagina de intel y miramos el user:
Flag: **flag{mort1cia}**

### Lets Begin
*Now that you've answered the starter questions, let's get on with the CTF! Enter this as the flag: flag{lets_get_started}*

Flag: **flag{lets_get_started}**

### UNCOMN
*Which Hacktober sponsor has the following mission statement:*
*We analyze, design and deliver customized people, process, technology, and data solutions to solve your most complex business challenges.*
*Submit the flag as flag{Company_Name}.*

Flag: **flag{UNCOMN}**

### Core Values
*How many core values are listed on UNCOMN's website? Submit the flag as flag{#}*

Flag: **flag{5}**

## SQL
### Past Demons
*We've had a hard time finding anything on spookyboi. But finally, with some search engine finessing, an analyst found an old, vulnerable server spookyboi used to run. We extracted a database, now we need your help finding the password. Submit the password as the flag: flag{password}.*
Nos descargamos el archivo y:
```
sqlite3 out.db 
sqlite> .tables
passwd  users 
sqlite> SELECT * from passwd
   ...> ;
1|4E6C0DBCCA0E45C805CE753C5974B3F9|1
2|8D302A5C9E06C8A6A52778A09583FD2C|2
3|3AC0D175A50406327CBE0BA0C6675892|3
4|6D5A4277C1F826D5EAAF08F63AEC84C5|4
5|CDF78099FBBBB52BE1AAA086D60289BC|5
6|66E85956792A2BEE9AA95B6F2662297F|6
7|EF468A19E03DDFD6D91ACF6602F71AF9|7
8|59DEA36D05AACAA547DE42E9956678E7|8
9|EB62D05A31866DFF8EC4EF28BAEF9377|9
10|2E8FF4B113C64A3C4B3F9D53AB1F0C53|10
```
Metemos los hashes en crackstation y vemos que uno de ellos se corresponde a la password zxcvbnm
Flag: **flag{zxcvbnm}**

### Address Book
*Shallow Grave University has provided us with a dump of their database. Find luciafer's email address and submit it as the flag in this format: flag{username@email.com}*
Ahora nos dan un dump de una base de datos hecho con sqldump, como nos piden el correo de luciafer buscamos por lucia y solo hay una 
```
(49,'luchav1987','LUCIA','HAVRON','R','luc1afer.h4vr0n@shallowgraveu.com','2991 Y Alley','Broken Bow',38,'27856','f','1987-12-13')
```
Flag: **flag{luc1afer.h4vr0n@shallowgraveu.com}**
### Body Count
*How many users exist in the Shallow Grave University database? Submit the flag in the following format: flag{#}*
Importamos el dump a una db local y vemos las tablas
```
+---------------------+
| Tables_in_hacktober |
+---------------------+
| countries           |
| courses             |
| degree_types        |
| enrollments         |
| passwords           |
| payment_statuses    |
| programs            |
| roles               |
| roles_assigned      |
| states              |
| term_courses        |
| terms               |
| users               |
+---------------------+
```
Ejecutamos la siguiente consulta:
```
select user_id from users;
```
Esto nos devuelve 900 rows

Flag: flag{900}
### Fall Classes
*Without counting duplicates, how many courses are being offered in the FALL2020 term at Shallow Grave University? Submit the flag in the following format: flag{#}*
Bien nos piden los cursos para un term , en term_courses hay un campo con el termi_id consultemos la tabla de terms para ver cual queremos
```
+---------+------------+------------+------------+----------------------+
| term_id | term_name  | start_date | end_date   | description          |
+---------+------------+------------+------------+----------------------+
|       1 | SPRING2020 | 2020-04-06 | 2020-07-20 | Spring semester 2020 |
|       2 | FALL2020   | 2020-08-03 | 2020-11-20 | Fall semester 2020   |
+---------+------------+------------+------------+----------------------+
```
Bien buscamos el term 2 , ahora construyamos la consulta
```
select distinct course_id  from term_courses where term_courses.term_id = 2;
401 rows in set (0.00 sec)
```
Flag: **flag{401}**
### Null and Void
*Using the Shallow Grave SQL dump, which field(s) in the users table accepts NULL values? Submit the field name followed by the single command used to show the information (separated by a comma). Submit the flag as flag{column-name, command}.*
```
mysql> describe users;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| user_id  | int         | NO   | PRI | NULL    | auto_increment |
| username | varchar(52) | NO   | UNI | NULL    |                |
| first    | varchar(52) | NO   |     | NULL    |                |
| last     | varchar(52) | NO   |     | NULL    |                |
| middle   | varchar(24) | YES  |     | NULL    |                |
| email    | varchar(52) | NO   | UNI | NULL    |                |
| street   | varchar(52) | NO   |     | NULL    |                |
| city     | varchar(52) | NO   |     | NULL    |                |
| state_id | int         | NO   | MUL | NULL    |                |
| zip      | varchar(10) | NO   |     | NULL    |                |
| gender   | varchar(8)  | NO   |     | NULL    |                |
| dob      | date        | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
```
Flag: **flag{middle, DESCRIBE}**

### Calisota
*One of our other analysts isn't familiar with SQL and needs help finding out how many users live in which states. Submit the SQL command used to get the total number of users in the users table who live in California and Minnesota.*
El estado se almacena en base a un id numerico , lo primero sera averiguar cuales son los que necesitamos
```
mysql> SELECT * from states WHERE states.state_full = "California" OR states.state_full = "Minnesota" ;
+----------+------------+--------------+------------+
| state_id | state_full | state_abbrev | country_id |
+----------+------------+--------------+------------+
|        6 | California | CA           |        199 |
|       28 | Minnesota  | MN           |        199 |
+----------+------------+--------------+------------+
```
Ahora construyamos la consulta en base a esto:
```
SELECT COUNT(*) FROM users WHERE users.state_id = 6 OR users.state_id = 28;
```
Flag **flag{SELECT COUNT(*) FROM users WHERE state_id=6 OR state_id=28;}**
## OSINT
### Creeping 1
*Ali Tevlin is quite active on Ghost Town and we believe he's behind some of the recent attacks on De Monne Financial. See what you can find out about him on the internet - it might give us an idea about why he's targeting De Monne Financial.What company does Ali Tevlin work for?*
Hacemos un dorking para "Ali Tevlin" y encontramos un perfil de facebook donde vemos que trabaja en la compañia F. Kreuger Financial

Flag: **flag{F. Kreuger Financial}***
### Creeping 2
*Based on what you've been able to discover about Ali Tevlin, tell us what his position is at his current company.*
En el mismo punto del perfil de facebook pone el puesto
Flag: flag{Senior Acquisitions Supervisor}

## Trafic Analysis
### Evil Corp's Child
*What is the MD5 hash of the Windows executable file?*

Flag: **flag{a95d24937acb3420ee94493db298b295}**
### Evil Corp's Child 2
*The malware uses four different ip addresses and ports for communication, what IP uses the same port as https? Submit the flag as: flag{ip address}.*

Flag **flag{213.136.94.177}**

### Evil Corp's Child 3
*What is the localityName in the Certificate Issuer data for HTTPS traffic to 37.205.9.252?*
Como buscamos un certiicado emitido por esa IP aplicamos el siguiente filtro:
```
ip.src == 37.205.9.252 && tls
```
Esto nos deja 5 paquetes ,el primero de ellos contiene el certificado:
```
521	37.205.9.252	192.168.1.91	TLSv1.2	1381	Server Hello, Certificate, Server Key Exchange, Server Hello Done	56109
```
Si vemos en profundidad veremos un campo llamado localityName en el pone Mogadishu
Flag flag{Mogadishu}
### An Evil Christmas Carol
*A malicious dll was downloaded over http in this traffic, what was the ip address that delivered this file?*
Aplicamos http como filtro en wireshark y vemos esto:
```
111	10.0.0.163	205.185.125.104	HTTP	533	GET /files/july22.dll HTTP/1.1 	80
```
Flag **flag{205.185.125.104}** 
### An Evil Christmass Carol
**
Encontramos un (reporte)[https://www.hybrid-analysis.com/sample/c1532b3d37ff2ec7d70d7f8037b8cdf843d3cdd24adf860f4251d045ddf9d47c?environmentId=100] de la dll que nos da una IP: 84.38.181.206, aplicamos un filtro sobre esta IP en wireshark
```
ip.dst== 84.38.181.206 
```
Y nos devuelve paquetes,asi que parece un buen camino a seguir, dado que buscamos un dominio, quitemos este filtro y veamos las consultas dns, veremos una sobre esta IP
```
830	10.0.0.1	10.0.0.163	DNS	155	Standard query response 0x6c5c A vlcafxbdjtlvlcduwhga.com A 84.38.181.206 NS a.dnspod.com NS b.dnspod.com NS c.dnspod.com	52518
```
Ahí tenemos nuestro dominio vlcafxbdjtlvlcduwhga.com

Flag **flag{vlcafxbdjtlvlcduwhga.com}**
### Remotely Administrated Evil
*What is the name of the executable in the malicious url? Submit the filename as the flag: flag{virus.bad}.*
```
6	172.16.1.219	45.40.135.135	HTTP	125	GET /ot/solut.exe HTTP/1.1 	80
```
Flag: **flag{solut.exe}**

### Remotely Administrated Evil 2
*What MYDDNS domain is used for the post-infection traffic in RATPack.pcap?*
Filtramos paquetes de DNS ya que buscamos un domain
```
1497	172.16.1.219	172.16.1.1	DNS	78	Standard query 0xb6b0 A solution.myddns.me	53
```
Flag **flag{solution.myddns.me}**

## Programming
### Message in an Array
*Deadface has left a message in the code. Can you read the code and figure out what it says? You may also copy and paste the code in an emulator. Enter the answer as flag{Word Word Word Word}*

Vemos el code:
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace GhostTown
{
    class Program
    {
        static void Main(string[] args)
        {
           string[] str = new string[4] {"DEADFACE","Nothing", "Stop", "Will"};

           Console.WriteLine("{1} {3} {2} {0}", str);
         }
    }
```
Creo que es javascript, imprimira Nothing Will Stop DEADFACE 
Flag **flag{Nothing Will Stop DEADFACE}**

### Trick or Treat
*We found a script being used by DEADFACE. It should be relatively straightforward, but no one here knows Python very well. Can you help us find the flag in this Python file?*
Vemos el script
```
from hashlib import md5 as m5


def show_flag():
    b = 'gginmevesogithoooedtatefadwecvhgghu' \
        'idiueewrtsadgxcnvvcxzgkjasywpojjsgq' \
        'uegtnxmzbajdu'
    c = f"{b[10:12]}{b[6:8]}{b[4:6]}{b[8:10]}" \
        f"{b[4:6]}{b[12:14]}{b[2:4]}{b[0:2]}" \
        f"{b[14:16]}{b[18:20]}{b[16:18]}{b[20:22]}"
    m = m5()
    m.update(c.encode('utf-8'))
    d = m.hexdigest()
    return f"flag{{{d}}}"


def show_msg():
    print(f'Smell my feet.')


show_msg()
```
Si cambiamos la ultima linea por print(show_flag()) nos da la flag

Flag: **flag{2f3ba6b5fb8bb84c33b584f981c2d13d}**
### Haunted Mirror
*We found a script being used by DEADFACE. One of our informants says that the code contains one of mort1cia's passwords. There must be a way to get it out of the file.*
Probe a tirar de strings y nada util, con ghidra y cutter tampoco vi nada en claro, pero probando format strings la cosa cambia:
```
./mirror %s%s%s
Hello, stranger. I'm trapped behind your screen. Type any word and I'll write it back to you from the other side. Say the right word, and I'll tell you a secret.
flag{XQwG1PhUqJ9A&5v}
s%s%s%
```
Flag **flag{XQwG1PhUqJ9A&5v}**

### Red Rum
*We want you to infiltrate DEADFACE as a programmer. Thing is, they're picky about who they bring in. They want to make sure you're the real deal when it comes to programming. Generate a list of numbers 1-500. For each number divisible by 3, replace it with Red; for each number divisible by 5, replace it with Rum. For numbers divisible by both 3 AND 5, replace it with RedRum.*
Cuando nos conectamos al servidor nos devulve lo siguiente:
```
DEADFACE gatekeeper: If you want to join our programmers circle, you need to show that you can at least do the basics. Send the first 500 (1 - 500) Red Rums to show you're serious. You answer should be comma-separated with no spaces.
```
Esa informacion es bastante importante sobre el formato, ahora escribimos un script rapidito y listo
```
payload=""
for i in range (1,501):
	flag=0
	if (i%3 == 0):
		payload+="Red"
		flag=1
	if (i%5 == 0):
		payload+="Rum"
		flag=1
	if not(flag):
		payload+=str(i)
	if (i != 500):
		payload+=","	
print(payload)
```
Mandamos dicho payload
```
python payload.py | nc env2.hacktober.io 5000
```
Y el servidor nos devuelve la flag
Flag: **flag{h33eeeres_j0hnny!!!}**
## Forensics
### Captured memories
*We found some unusual activity coming from an employee's Windows 10 workstation at De Monne Financial. Our IT guy saved the memory dump to the file provided below. What was the PID of the program used to capture the memory dump? Submit the flag as flag{PID}.*
```
volatility -f file imageinfo
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win10x64_10586, Win10x64_14393, Win10x64, Win2016x64_14393
                     AS Layer1 : Win10AMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/pj/Current/Hacktober/mem.raw)
                      PAE type : No PAE
                           DTB : 0x1aa000L
                          KDBG : 0xf8001e43d520L
          Number of Processors : 2
     Image Type (Service Pack) : 0
                KPCR for CPU 0 : 0xfffff8001d4e2000L
                KPCR for CPU 1 : 0xffffd40032268000L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2020-06-26 15:51:36 UTC+0000
     Image local date and time : 2020-06-26 08:51:36 -0700
```
Ahora con el perfil claro miramos los procesos:
```
volatility -f mem.raw --profile=Win10x64_10586 pslist
0xffff87868f2e1078 ????????winpmem_       3348    332 23...6        0 ------      0 6236-07-21 07:00:39 UTC+0000                                 
```
Flag: **flag{3348}**

### Prefetch Perfection
*Prefetch files are another handy tool to show evidence of execution. What time was Internet Explorer opened? (GMT) Submit the flag as flag{YYYY-MM-DD HH:MM:SS}.*
Usando esta [tool](https://github.com/PoorBillionaire/Windows-Prefetch-Parser) es pan comido
```
prefetch.py -e ./ | grep IEXPLORE
2017-05-01 21:11:41.058966, IEXPLORE.EXE-F6A52C86
```
Flag: **flag{2017-05-01 21:11:41}**
### Prefetch Perfection 2
*One of the processes loaded a cookie file belonging to cmaldonado, what is the name of the process? Submit the flag as flag{process.exe}*
```
prefetch.py -f IEXPLORE.EXE-F6A52C86.pf | grep CMALDONADO | grep COOKIES
    \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES
    \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW
59:   \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\INDEX.DAT
146:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\INDEX.DAT
179:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@MSN[1].TXT
182:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@WWW.MSN[2].TXT
193:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@AT.ATWOLA[1].TXT
194:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@AT.ATWOLA[2].TXT
199:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@ATWOLA[2].TXT
224:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@PIXEL.RUBICONPROJECT[1].TXT
234:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@ADTECHUS[1].TXT
235:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@NEXAGE[1].TXT
263:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@MICROSOFT[2].TXT
264:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@MICROSOFT[1].TXT
265:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@C.BING[1].TXT
266:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@C.BING[2].TXT
267:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@C1.MICROSOFT[2].TXT


prefetch.py -f FTK\ IMAGER.EXE-3FF3CBAB.pf | grep CMALDONADO | grep COOKIES
    \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES
79:   \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\INDEX.DAT


prefetch.py -f DLLHOST.EXE-6202E8F2.pf | grep CMALDONADO | grep COOKIES
    \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES
    \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW
50:   \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@WWW.MSN[1].TXT
297:  \DEVICE\HARDDISKVOLUME1\USERS\CMALDONADO\APPDATA\ROAMING\MICROSOFT\WINDOWS\COOKIES\LOW\CMALDONADO@MSN[1].TXT
```

Flag: **flag{dllhost.exe}**
## Linux
### Talking to the death 1 & 2 & 3 & 4 
*Submit the contents of flag3.txt from the remote machine.*
```
find / -perm /4000 2> /dev/null
/usr/local/bin/ouija
```
Veamos con strings que hace el binario
```
OUIJA 6.66 - Read files in the /root directory
Usage: ouija [FILENAME]
EXAMPLES:
	ouija file.txt
	ouija read.me
```
Bien sabiendo esto solo necesitamos encontrar los archivos con las flags:
```
find / -name *flag* 2> /dev/null
/home/luciafer/Documents/.flag2.txt
/home/luciafer/Documents/flag1.txt
/home/spookyboi/Documents/flag3.txt
/root/flag4.txt
```
Y solo nos queda leerlos usando la ouija siempre teniendo en cuenta que es desde /root
```
ouija ../home/luciafer/Documents/.flag2.txt
ouija ../home/luciafer/Documents/flag1.txt
ouija ../home/spookyboi/Documents/flag3.txt
ouija flag4.txt
```
Con eso ya tendriamos las 4 flags

## Crypto
### Cover your bases
*One of the other junior analysts stumbled upon this and isn't quite sure what to make of it. Based on the context that it was found, it might be a coded message.Decode this: ZmxhZ3tzaG91bGRhX21hZGVfdGhpc19vbmVfaGFyZGVyfQ==*

Decodificamos el base64 y listo
Flag **flag{shoulda_made_this_one_harder}**
