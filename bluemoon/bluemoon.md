![[Pasted image 20240305120542.png]]
Primero miraremos que puertos tiene abiertos, al ver que tiene el http abierto pues deduciremos que tiene una web corriendo.
![[Pasted image 20240305120639.png]]

miraremos que archivos esconde
![[Pasted image 20240305121326.png]]
vemos que hay un directorio oculto en la web.
![[Pasted image 20240305121421.png]]
al mirar el codigo fuento vemos en la parte de abajo un link hacia un qr.
entramos a esta web para mirar el contenido oculto.
https://zxing.org/w/decode.jspx
![[Pasted image 20240305121658.png]]
vemos que nos da las credenciales del usuario ftp.
![[Pasted image 20240305122128.png]]
entramos al usuario ftp, vemos que tiene dos archivos, los descargamos y los abrimos en nuestro kali.
![[Pasted image 20240305122308.png]]con esto encontramos que el usuario se llama robin y un diccionario de contraseñas.
![[Pasted image 20240305122459.png]]
Realizamos un ataque de dicionario para ver cual es la contraseña.
![[Pasted image 20240305123257.png]]
vemos que tenemos esta serie de permisos, ademas ya sabemos el usuario administrador, es jerry![[Pasted image 20240305123528.png]]
para escalar usaremos el siguiente comando para poder acceder al usuario jerry
![[Pasted image 20240305123616.png]]
vemos que una vez ejecutado seremos jerry
![[Pasted image 20240305123829.png]]
nos dirigimos en la carpeta de jerry y veremos la segunda flag.
