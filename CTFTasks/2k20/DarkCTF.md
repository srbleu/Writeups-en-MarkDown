# DarkCTF
## Web
### MrRobot 
Accedemos a robots.txt y vemos lo siguiente
```
Disallow : /l33t_hax0r.html
```
Veamos esa url y podemos ver la flag

**darkCTF{maybe_i_should_give_you_a_name}**

### Web 1
La pagina nos lo dice muy clarito
```
To login use /login and btw I love post req only :p
ps: set header for specifying json
```
Hagamoslo
```
curl -X POST --header "Content-Type: application/json" -d '{"user":"admin","pass":"karma"}' http://web1.darkarmy.xyz/login
{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.bJD0yEzriewpce-NWd1iummQBkQjOl_6qt8bPr9m8gs"}
```
Probemos el token en /flag
```
 curl -X POST --header "Content-Type: application/json" -d '{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.bJD0yEzriewpce-NWd1iummQBkQjOl_6qt8bPr9m8gs"}' http://web1.darkarmy.xyz/flag
```
Nos dice que no somos admin, probemos a modificar el JWT para que en vez de tener user:guest , tengamos user:admin y repitamos la request
```
 curl -X POST --header "Content-Type: application/json" -d '{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4ifQ.bJD0yEzr_6qt8bPr9m8gs"}' http://web1.darkarmy.xyz/flag
 ```
Esto nos da el flag **darkCTF{345y_p345y_JWTs}**

## Crypto
### Really Secure Algo
```
n = 977777777777777777777777777777777777777777777777777777777777777777777777
777777777777777777777777777777777777777777777777777777777777777777777
e = 65537
c = 777100860344719251862233099079929815950590358671816920785821983126
256200182002548130383416252558510764139287733939898022176140368825482344800
```
Es un RSA sencillito, probemos con RSACTFTool
```
python RsaCtfTool.py -n 977777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777 -e 65537 --uncipher 777100860344719251862233099079929815950590358671816920785821983126256200182002548130383416252558510764139287733939898022176140368825482344800
```
Con esto obtenemos la flag

**darkCTF{CTF_without_rsa?_pttf}**


## Forensic
### 1by2
```
1 / 2
walking over the bins...
meta meta meta...
```
Tiramos de binwalk y extraemos un zip, de este zip una imagen, y en los metadatos de la imagen tenemos lo siguiente
```
darkCTF{Half_
```
La otra mitad de la flag esta escrita en la primera imagen

```
4nd_h4Lf}
```
**darkCTF{Half_4nd_h4Lf}**

### RGBA
Sabemos que se ha aplicado esteganografía en el canal Alfa, siendo un PNG zsteg es nuestro primer recurso
```
zsteg -a chall.png | grep darkCTF
```
Y nos da la flag
**darkCTF{th3_fl4g_1s_tran5par3nt}** 

### SS
QQSTV Decoding  pero con la app del movil, ya que con el qsstv por algun motivo no decodifica bien y obtenemos la flag

**darkCTF{s10w_Sc4n_t3l3v!s!on}**

## Misc
## Pull, Push , Commit
*Flag on karma9874, can you find it? xD*

Buscamos ese user en github y encontramos un repo asociado llamado flaaaaaaaaaaaaaaaag, en el hay un archivo llamado flag.c con el siguiente codigo

``` C
#include <stdio.h>
#define HN {
#define IB }
#define Bl8 int
#define i1 (
#define i0 )
#define g6 [
#define g7 ]
#define T4 =
#define T9 +
#define GA4 while
#define GA9 <
#define JG6 char
#define bye return
#define l6 ,
#define DU printf
#define BP ;
Bl8 main i1 i0 HN
    Bl8 a g6 31 g7 T4 HN 100 l6 97 l6 114 l6 107 l6 67 l6 84 l6 70 l6 123 l6 110 l6 48 l6 112 l6 51 l6 95 l6 55 l6 104 l6 49 l6 53 l6 95 l6 110 l6 48 l6 55 l6 95 l6 55 l6 104 l6 51 l6 95 l6 102 l6 49 l6 52 l6 57 l6 125 IB BP Bl8 i T4 0 BP Bl8 j T4 1 BP GA4 i1 i GA9 31 i0 HN DU i1 "%c" l6 i1 JG6 i0 a g6 i g7 i0 BP i++ BP IB bye 0 BP IB
```
Vamos a convertirlo en algo legible
```C
#include <stdio.h>

int main ( ) {
    int a [ 31 ] = { 100 , 97 , 114 , 107 , 67 , 84 , 70 , 123 , 110 , 48 , 112 , 51 , 95 , 55 , 104 , 49 , 53 , 95 , 110 , 48 , 55 , 95 , 55 , 104 , 51 , 95 , 102 , 49 , 52 , 57 , 125 } ; int i = 0 ; int j = 1 ; while ( i < 31 ) { printf ( "%c" , ( char ) a [ i ] ) ; i++ ; } return 0 ; }
```
Obtenemos el siguiente output darkCTF{n0p3_7h15_n07_7h3_f149}

Rabbit hole por lo que se ve

Vamos a los issues y vemos uno relaccionado con este archivo  que modifica un poco el codigo
```
https://github.com/karma9874/flaaaaaaaaaggg/pull/1/commits/4d1d4dcbbe65eb8b79646ee52f981de169539359?file-filters%5B%5D=
```
Ejecutamos el codigo modificado y nos da la flag

**darkCTF{PR_134k_1nf05_t00}**

### Flag inception

Nos dan un zip con un monton de zips
```python
for i in range(0,1000):
  os.system("find /home/pj/Current/Dark -name "+ str(i) + ".zip -exec unzip {} \;")
```
Al extraer tenemos un monton de directorios pero en el ultimo no hay flag hmmmm, veamos si hay alguna pista
```
ls -laR | sort | uniq -u | grep flag
>-rw-r--r--   1 pj pj    20 ago 14 19:52 flag.txt
>-rw-r--r--   1 pj pj    38 ago 14 19:52 .flag
```
Ese .flag es sospechoso
```
find . -name .flag 
>./69/.flag
```
En el esta la flag
**darkCTF{f1n411y_y0u_h4v3_d4_r341_f14g}**

## OSINT


### Perception
*Did you see all our promotions in detail if not. go see again and don't forget to follow us on social media.*

Buscamos las promociones, y viendo el video de Youtube vi un QR sobre el segundo 16, si lo escaneamos obtenemos la flag

**darkCTF{6o1n6_7hr0u6h_7h3_Fr4m3s}**

### Catch me if you can
Buscamos por imagen y encontramos esa foto en otra URL
```
http://www.gc-gapa.de/partner-hotels-und-clubs/
```
Si buscamos la foto veremos el nombre del edificio

**darkCTF{das_posch_hotel}**

### Mr n00bs channel
Mirando los exif de la foto vemos un comentario
```
User Comment                    : mrn00b1337
```
Buscamos el user y encontramos una cuenta de twitter, en esa cuenta encontramos un canal de youtube
```
https://www.youtube.com/channel/UCfiPiuhOt5ctM0LQWAJeNYg
```
Solo tiene un video de la escena de castle super Haxxor pero con maullidos, si miramos las tags del video vemos la flag, para ver las tags miramos en el fuente

**flag : darkCTF{t4gs_4re_1mportant}**

