## Welcome
### Welcome letter
*Read the letter!*
```
Dear Sir or Madam,

Welcome to your job position at BB Industry a.s., the leading manufacturer of filling technology and blowmolding.

For the next five days, our German subsidiary Senork Vertriebs GmbH and the security experts of ourProduct Security Incident Response Team (PSIRT) will forward every security task that they can’t solve.

You have to consider that we are in straitened circumstances due to the ongoing global pandemic.If we see that you can solve the easy tasks on your first day (Monday), we will gradually continue to sendmore challenging tasks (Friday).

You have to send us a flag as proof that you solved the task. Usually, the format will besyskronCTF{...};however, there is a unique format in some cases. Therefore, read the description of each task carefully. Weneed reliable security people at BB Industry.

Once again, thank you for joining our great company. Please confirm that you read the letter by enteringthe flag on this page
```
La flag esta en la cabecera del archivo
Flag **syskronCTF{th4nk-you}**

## Trivia
### Vulnerable RTOS
*What are the names of 11 zero-day vulnerabilities that affect a wide range of real-time operating systems?*

Buscamos RTOS vulns y encontramos esta [noticia](https://www.helpnetsecurity.com/2019/07/29/vxworks-rtos-vulnerabilities/)

Flag **syskronCTF{urgent11}**

### Check digit
*We need a check digit in our keys. What do IMEI numbers, credit card numbers and UIC wagon numbers have in common? Flag format: syskronCTF{relevant-ISO/IEC-standard}*

Los IMEY y las CC tienen en comun el algoritmo usado para la verificacion que yo sepa, buscando hallamos que los numeros de los UIC wagons tambien usan el algorimo de luhn, buscamos su ISO y listo

Flag **syskronCTF{ISO/IEC-7812}**

### Deadly malware
*Which malware targets safety systems of industrial networks and caused deaths due to explosion?*

Flag **syskronCTF{Triton}**

## Monday
### DoS attack 
*One customer of Senork Vertriebs GmbH reports that some older Siemens devices repeatedly crash. We looked into it and it seems that there is some malicious network traffic that triggers a DoS condition. Can you please identify the malware used in the DoS attack? We attached the relevant network traffic.*


Flag **syskronCTF{Industroyer}**

### Security headers 
*Can you please check the security-relevant HTTP response headers on www.senork.de. Do they reflect current best practices?*
Fue cuestion de probar metodos ya que OPTIONS no estaba habilitado
```
curl -I  https://www.senork.de/ | grep CTF
flag-policy: syskronCTF{y0u-f0und-a-header-flag}
```
Flag: **syskronCTF{y0u-f0und-a-header-flag}**## Welcome
### Welcome letter
*Read the letter!*
```
Dear Sir or Madam,

Welcome to your job position at BB Industry a.s., the leading manufacturer of filling technology and blowmolding.

For the next five days, our German subsidiary Senork Vertriebs GmbH and the security experts of ourProduct Security Incident Response Team (PSIRT) will forward every security task that they can’t solve.

You have to consider that we are in straitened circumstances due to the ongoing global pandemic.If we see that you can solve the easy tasks on your first day (Monday), we will gradually continue to sendmore challenging tasks (Friday).

You have to send us a flag as proof that you solved the task. Usually, the format will besyskronCTF{...};however, there is a unique format in some cases. Therefore, read the description of each task carefully. Weneed reliable security people at BB Industry.

Once again, thank you for joining our great company. Please confirm that you read the letter by enteringthe flag on this page
```
La flag esta en la cabecera del archivo
Flag **syskronCTF{th4nk-you}**

## Trivia
### Vulnerable RTOS
*What are the names of 11 zero-day vulnerabilities that affect a wide range of real-time operating systems?*

Buscamos RTOS vulns y encontramos esta [noticia](https://www.helpnetsecurity.com/2019/07/29/vxworks-rtos-vulnerabilities/)

Flag **syskronCTF{urgent11}**

### Check digit
*We need a check digit in our keys. What do IMEI numbers, credit card numbers and UIC wagon numbers have in common? Flag format: syskronCTF{relevant-ISO/IEC-standard}*

Los IMEY y las CC tienen en comun el algoritmo usado para la verificacion que yo sepa, buscando hallamos que los numeros de los UIC wagons tambien usan el algorimo de luhn, buscamos su ISO y listo

Flag **syskronCTF{ISO/IEC-7812}**

### Deadly malware
*Which malware targets safety systems of industrial networks and caused deaths due to explosion?*

Flag **syskronCTF{Triton}**

## Monday
### DoS attack 
*One customer of Senork Vertriebs GmbH reports that some older Siemens devices repeatedly crash. We looked into it and it seems that there is some malicious network traffic that triggers a DoS condition. Can you please identify the malware used in the DoS attack? We attached the relevant network traffic.*


Flag **syskronCTF{Industroyer}**

### Security headers 
*Can you please check the security-relevant HTTP response headers on www.senork.de. Do they reflect current best practices?*
Fue cuestion de probar metodos ya que OPTIONS no estaba habilitado
```
curl -I  https://www.senork.de/ | grep CTF
flag-policy: syskronCTF{y0u-f0und-a-header-flag}
```
Flag: **syskronCTF{y0u-f0und-a-header-flag}**
