
![[Pasted image 20240303151419.png]]
Hacemos un escaneo de puertos abiertos, aquí podemos ver que es un Windows XP y que tiene el puerto 445 abierto. Lo que podemos intentar es encontrar una vulnerabilidad. Para ello realizarwemos el siguiente escaneo para ver que vulnerabilidades tiene ese puerto.
![[Pasted image 20240303151640.png]]

vemos que nos indica la vulnerabilidad y que tiene su cve relacionado, por lo tanto abriremos metasploit y buscaremos un exploit para esa vulnerabilidad.
![[Pasted image 20240303151742.png]]
![[Pasted image 20240303151807.png]]
elegimos el número 1. Para seleccionarlo usamos el siguiente comando.
![[Pasted image 20240303151853.png]]
Luego configuramos la ip destinataria
![[Pasted image 20240303151922.png]]
finalmente lo ejecutamos
![[Pasted image 20240303152021.png]]
Con esto ya estariamos dentro, para ejecutar la consola del windows usaremos el siguiente comando.
![[Pasted image 20240303152110.png]]
Finalmente nos dirigiremos al escritorio y ahi enconraremos las flags.
![[Pasted image 20240303152203.png]]
miramos lo que hay dentro de la carpeta
![[Pasted image 20240303152226.png]]
vemos que hay las flags, para mostrar su conetnido en consola ejecutaremos lo siguiente
![[Pasted image 20240303152305.png]]

