# HDC

*We believe a certain individual uses this website for shady business. Can you find out who that is and send him an email to check, using the web site's functionality?*

Mirando el codigo fuebte venis dos inputs ocultos
```
  <input type="hidden" value= name="name1">
  <input type="hidden" value= name="name2">
```
Y dos scripts js
```
<script src="jquery-3.2.1.js"></script>
<script src="myscripts.js"></script>
```
El segundo no tiene mucho interes
```javascript
function doProcess()
{
	document.forms["formaki"].submit();
}
```
Pero el primero nos aporta bastante
```
function doProcess() 
{var form=document.createElement("form");	
form.setAttribute("method","post");	
form.setAttribute("action","main/index.php");	
form.setAttribute("target","view");	
var hiddenField=document.createElement("input");	
hiddenField.setAttribute("type","hidden");	
hiddenField.setAttribute("name","name1");	
hiddenField.setAttribute("value","TXlMaXR0bGU");	
var hiddenField2=document.createElement("input");	
hiddenField2.setAttribute("type","hidden");	
hiddenField2.setAttribute("name","name2");	
hiddenField2.setAttribute("value","cDB3bmll");	
form.appendChild(hiddenField2);		
form.appendChild(hiddenField);	
form.appendChild(hiddenField2);	
document.body.appendChild(form);			
window.open('','view');	form.submit();}
```
Tenemos los valores de los dos field, con esto podemos pasar al segundo paso. 

Ahora estamos dentro de el area para clientes, como no debemos de olvidar la tarea que tenemos, la de encontrar un mail, vayamos a la sección del mailbox.
Allí vemos una foto cuya ruta revela una carpeta a tener en cuenta:
```
http://167.172.52.58:32339/main/secret_area_/mails.gif
```
Si accedemos a la carpeta anterior podemos ver que en la carpeta de secret_area_ hay un archivo mas 
```
All good boys are here... hehehehehehe!
----------------------------------------
Peter Punk CallMePink@newmail.com
Nabuchodonosor BabyNavou@mailpost.gr
Ilias Magkakos imagkakos@badmail.com
Nick Pipshow NickTheGreek@mail.tr.gr
Don Quixote Windmill@mail.gr 
Crazy Priest SeVaftise@hotmail.com
Fishroe Salad fishroesalad@mail.com
TaPanta Ola OlaMaziLeme@mail.gr
Laertis George I8aki@mail.gr
Thiseas Sparrow Pirates@mail.gr
Black Dreamer SupaHacka@mail.com
Callme Daddy FuckthemALL@mail.com
Aggeliki Lykolouli FwsStoTounel@Traino.pourxetai
Kompinadoros Yannnnis YannisWith4N@rolf.com
Serafino Titamola Ombrax@mail.gr
Joe Hard Soft@Butter.gr
Bond James MyNameIsBond@JamesBond.com
Endof Text EndOfLine@mail.com
```
Tenemos tambien un endpoint para enviar emails, quizas sea buena idea intentar usar este endpoint para mandar emails a estos.
Si cazamos una request con burpsuite al hacelo y probamos con la lista de mails eventualmente obtendremos la flag

Flag: **HTB{FuckTheB3stAndPlayWithTheRest!!}**
