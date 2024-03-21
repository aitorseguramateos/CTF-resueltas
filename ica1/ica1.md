

![](https://lh7-us.googleusercontent.com/qki8YJknNpMTWcB_Vw9kAQJZLEXwj7S0qZPSZvMi5HaUfSd-oPWoFH_P4uBUxLMzrIdeuNg4CT06wVI1PZuIf-bQkHsbdflmRgDnj2OeTGOJHlGavgxrNJRJMxhJhLZgAu7CagLy5wtx1uTwlysQAjg)

Una vez escaneado entramos a la web.

![](https://lh7-us.googleusercontent.com/QMp1MKCphSxbJBUFGjebA-Rmuo7WB7t_vDq7t2vZQUjDQPp4_abO59hvj06-8t_QqM5L8wLSfCQeLAAWsFgYXb48ZNIilrvOOsYtO_5E_QvQQ5n_sAqLpq9HwR81mN69WDnD6c6qQRjZ2T7B0oQO19k)

Vemos que tiene una versión.

Miraremos si es muy antigua.

Vemos que esa versión tiene un exploit

El la URL añadiremos “core/config/databases.yml”.

Se nos descargara un archivo con las credenciales.

![](https://lh7-us.googleusercontent.com/MqkJ_l0mWxpHKkeVFAMBu9VXhj6s8dH6Ur2Jmdnme2Gr9ONFjLB942Pz7z8OXN_Gj3mkcL7voE6wDXqalFkR1Nm8_5VY7DiYKeIPF-Rrmo9m__dmB1_Ld_Y0l1EdjU5dq_qKpv3VlSlScRPpwWCldpg)

entramos a la base de datos vía terminal.

![](https://lh7-us.googleusercontent.com/WysyNa_wOCLw7G4Bla4_xVccvzfCr4OIJWaAdB9qVQ7v2uHxqnDKiAlhDAk7Cdmsm-O5kyi3CzUenLoXHjJDasiyRTYzxsrMRdqRg6qrbl9zDgXWOTj9Bgmm91ZMCc_mYIAZ5-XxRIYo21jY1jb3lnQ)

![](https://lh7-us.googleusercontent.com/QO0rgu_ceiN2x1-8qDvZTFLhvwfpX8B1xGCSTjFhr3cJvfWJM-nc8cbYFvuIJtBoYxv_avMle-6E48I31oYDEVXQn_heqkli_VHKQj0svILr2TmyN3XZoN9htRBxdOVqGFbbHYHv9LdiZDj8e1RXVM8)

Miraremos qué DB tiene. Vemos que hay una que se llama staff. 

Miraremos que tablas tiene

![](https://lh7-us.googleusercontent.com/nZyGlLYe22ePkXK435CeMrxXn5ke7ZIguxb6LLiQ6aBKtPh3e8cUTJbbMeH7_yKdU5iYQW4mP3OmofM_11NZc9BCmDGV7KnpwHfcqUElwcBf1Bw9OEXyJL82cGwygEpMfau4jdkXVAhqihw0UVFQSoM)

Entramos a la login haciendo una consulta con el select.

![](https://lh7-us.googleusercontent.com/ZuOdaJea2zFGa90pOmzs8DjbcZbD5tkHdBXemZ9W7POa3x22kDMU0WZgycmiEJ3ICRMMtMOq7SxeaSUSVYKoBVG5KZHttwDiiKjS7nYWvjez8iqXlqBhzBOjg0ApbODXVaTxvfktWZK_WfwXA5XPFII)

Esta esta hasheada via base 64. La decodificamos con un decoder y lo guardaremos en un documento.

![](https://lh7-us.googleusercontent.com/ft1hOoIX6qMtUk8u3kfTwgWQdPd1bDkurYU6fcpDkRDb5f8xLUPAxgHVNHjyFUFv1n_qmOy1kjZjJKQgY-6wWPaVhr95iJkh7eZdvSCD-4QDyboDPK4z6oQUqd32tru2s-SBRSjlpUz-PeHR61itNpo)

  

Ahora entraremos a la de user con un select.

![](https://lh7-us.googleusercontent.com/zt-1ZsPiDV3NskdLuS3jC-O66BqlGAzwZdpsp0ntAgbBKPqOmb3KudiQjWWjXv8yg7Ec7EdcpJE8t6TRMs3QjadrNfCH6XgCa6nEX95IpH3mqizbtYDPjUNwYLbTNZwSiawVF6kxy0s1KEvhnpU9Fvk)

Guardaremos sus nombres en minusculas en un documento.

![](https://lh7-us.googleusercontent.com/qqUdxgUkNmItMoLIYucTq6We9d9CVAsyganxgG8FQoJ3nyI390If_zK69pcEhLU_4h7IipScaEg9Imf8Ki6rxc9LIOd6zUZqusRGNO7rX10kkvldwhGw1baJZ8D1R00oNxvc6RyV5-Z02mJk3axESys)

  

Seguidamente realizaremos un ataque de fuerza bruta con hydra.

![](https://lh7-us.googleusercontent.com/LSnzrQrIvr7GrRaYv0XUUi62SFzbQ-Lf1DsFQzwJBAd1rC-YuNIdAKUOb29GqX88aY7DQHSOmTf-0tqJyLuTEzWkHKFFGPSRo315ERxrXa4flIi4tt9UaV_QJCDDyvYc49nhS47wbIHgypCy9Tyf2Xs)

Con esto ya podremos entrar via ssh a estos usuarios y escalar privilegios. 

  

A parte podemos volver a la base de datos y entrar en la DB qdpm.

![](https://lh7-us.googleusercontent.com/hMxKAFYMaXCNMAoFcxOi5S6mpmKIzIBVZi1XngttWzCqEPpLkdHozqdbxEG9mmrQgCgVMhgcLc5ZdUmaJQFqvTodrO6dHcZsPQO1FStAGZ27-0o0vV8o7jWiC01jQwKzzaFix_JEHATj2gSOiIXsAmA)

Miraremos que tablas tiene.

![](https://lh7-us.googleusercontent.com/stxDdnq9AKOWtM1eQEf93h-mG4dJrmAz5_JzPBCkBl3YjT1wsRiFipRdfALjo9lzkERFCL2mqiUxhqFY_lidnxziSkSd8US5SKmiBSG8I6ARrKvdAj8n1oSWnuZm4RZQRxGc4jOWqCmk5CNjdVffNzI)

Vemos una que nos llama la atención que se llama configuration. Haremos un select sobre ella para ver que esconde.

![](https://lh7-us.googleusercontent.com/wdVMnPyHyyUqOK7DLw7kyQsyLfXe7XV-qchzGOaY_q3gcS0h-qguxEctYQ70QkZBnbZsCWsGyWcFzOA7vOwcRSsNgdNyNvA2f44QardRr_LyUjGVmaGKBq2XduJJelklDNKsy38gLmNUC3-x-gl451w)

Vemos que esconde el correo de usuario y la contraseña hasheada. Seguidamente averiguaremos el hash y cambiaremos la contraseña.

[https://www.useotools.com/wordpress-password-hash-generator/output](https://www.useotools.com/wordpress-password-hash-generator/output)

  

![](https://lh7-us.googleusercontent.com/tKNCklWQCU3aOl4er20GLiOFEHo1yIvL01GnHNyNVLSV7noksH8oTv7-ZWRt6j7LCPaVw62GhtAkcU5Me3pgQS1enm5xJY8s0dLD0dxzvhQ3O4b_Ib3phHPXiMZkiTB5WrMbB685ZPm3MhLPG6QgY8E)

entramos como usuario administrador y creamos uno nuevo.

Luego entraremos y crearemos un proyecto y subiremos un archivo php malicioso para ejecutar una reverse shell.

luego vamos a la consola

![](https://lh7-us.googleusercontent.com/lS38bZ8s2ytSdG6zX4wpXYkNVtbmUgCTt0Z4MbqwzMskT8bBcynOBaVvtK_IuS0FvScUeMmoPKBPkztwL9FXZZazP6ewopga4UP3PVFPx7lKq6EYmGln1RiFFfs694W5Q0Ncrp7UER0kEecZk0UQsFo)

una vez estamos escuchando vamos a la siguiente url para ejecutar el php.

![](https://lh7-us.googleusercontent.com/ZT4osvnujRll5V_J8gsr_5N4qB58LqpcV_qoffaM2mCaosjTZsDlqPY3Z9K1RGqH-h2CdnfLa4qrDG0tTEacSbLtIpnhqtFMXsDYa832yeDd9qh_DR9XoPdxxQDfdvKTxM5R8F1-Lwqj-WR_moUu8Ls)

Una vez aquí pulsamos el archivo y automáticamente tendremos acceso al servidor y solo faltará escalar privilegios.

  

Para escalar los privilegios usaremos el siguiente comando para mirar permisos.

![](https://lh7-us.googleusercontent.com/a8QmsWOHVP9XBZzr7FW6fzckyZVZSWDg4rELymyyAFgrBM3lFjLvHDUsKt33eEJ_L4ryfIhgkjRcP9yBrOj_krBJta26Ba4KZA1ZdT1pPY1GizbrshgaMV_NOchFCkvRV29kuY7ZLTyobi94BQBw2Yk)

Vemos que nos llama la atención de /opt/get_acces

para ejecutar hay que realizar una serie de comandos

![](https://lh7-us.googleusercontent.com/lvpbV9IgcFFHhSgBibypTzXkHUWcwVFvE61bahfb_yh3hvDA0am_8ZaBAlpFF5iJ6BHCNjNfeKEE9ztp9mfb6ulH7duGwgTBKjUx2yjL1rNlDWQlxmNRIC7HIT4rvX0FQ93ckDKQf4PQfSa_lHfsy0o)

con esto ya seremos usuario root.