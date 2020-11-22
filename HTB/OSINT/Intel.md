# Intel
*It seems a huge trove of credit card details is being sold by a group going by the name flinchsec. Can you find any sites or artefacts associated with this group that we can use to detect them?*

Debemos encontrar un hilo del que tirar, la clave esta en **flinchsec**

Una busqueda rápida en google nos lleva a este [perfil de Linkedin](https://www.linkedin.com/in/ractor-burton-5179771b9/?originalSubdomain=uk).
Según Linkedin trabaja como Leader en Flinchsec y en la información de contacto del perfil encontramos una [web](w3h3lpp3opl3.tk/) que ya no esta disponible, busquemos en la Wayback Machine.

Encontramos un [snapshot](https://web.archive.org/web/20201030104512/http://w3h3lpp3opl3.tk/) de la página, en el encontramos un link a un [perfil de github](https://github.com/8e9c9259882e7e768160672f019971b1)

En los tags del unico repositorio encontramos un binario, si descargamos este y lo pasamos por VT o Hybryd Analisis lo detectara como malware, en VT desde la pestaña de de [detalles](https://www.virustotal.com/gui/file/e2d6bb50f10f5f108a4f9883e76cc970fc904e8c19d3bdaf410c8be68f9ade3c/details) podemos ver otros nombres con los que se ha subido el archivo.
```
HTB{051N7_F0R_M3}.exe
```

Ya tendríamos la flag

Flag: **HTB{051N7_F0R_M3}**
