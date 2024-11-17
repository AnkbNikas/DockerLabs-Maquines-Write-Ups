Maquina: Awful

Autor: AnkbNikas

Dificultad: Facil

Escaneo de la Red

El primer paso es asegurarnos de que tenemos conexión con la máquina objetivo. Esto se puede hacer lanzando un simple ping a la IP objetivo.

ping (IP) -c 1

Identificación de Puertos Abiertos
Usamos Nmap para identificar los servicios en los puertos abiertos con el siguiente comando:

Luego, identificamos los servicios en los puertos abiertos: sudo nmap -p- -sCV (IP) -Pn -n --min-rate 5000 

Encontramos los puertos 3306, 25, 22 & 80 abiertos

Exploración de la Página Web
Abrimos el navegador y navegamos a (IP). En la página principal, inspeccionamos el código y encontramos el mensaje:

Francis:“Me gustan los cisnes”, lo que nos da una pista del nombre de un usuario y de su contrasena.

Nos conectamos via SSH con las credenciales obtenidas.

ssh Francis@(IP)

Desde la cuenta de Francis, verificamos los permisos de sudo: 

sudo -l 

Observamos que Francis puede escalar privilegios a Darren usando el binario /env:

/usr/local/bin/env

Acceso al Escritorio de Darren
Accedemos al escritorio de Darren y encontramos un archivo user.txt con el siguiente mensaje:

recordatorio: mirar el email que me ha mandado Jefe con mis credenciales para poder acceder a mysql.

Captura de Tráfico con Wireshark

Nos ponemos a la escucha con Wireshark y capturamos el email enviado por Jefe con las credenciales de MySQL de Darren.

Acceso a MySQL

Usamos las credenciales capturadas para acceder a MySQL:

mysql -u Darren -p

USE user_data; 

SELECT * FROM user_info;

Obtención de Credenciales de Jefe

Encontramos las credenciales de Jefe en la base de datos y usamos estas credenciales para conectarnos por SSH:

ssh Jefe@(IP)

Búsqueda del Flag

Buscamos en el directorio Documents y encontramos el archivo Flag.txt.
