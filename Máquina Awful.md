Maquina: Awful

Autor: AnkbNikas

Dificultad: Facil

Escaneo de la red Siempre como primer paso revisamos que haya conexión con la maquina (objetivo), para esto lanzamos un simple ping a la ip objetivo que en este caso al correr la maquina nos ahorra el trabajo de localizar el host especifico que en caso de no encontrarlo podemos usar nmap -sn y escanear toda la red. 

ping (IP) -c 1

Luego, identificamos los servicios en los puertos abiertos: sudo nmap -p- -sCV (IP) -Pn -n --min-rate 5000 

Encontramos los puertos 3306, 25, 22 & 80 abiertos

Abrimos el navegador y navegamos a (IP). En la página principal, inspeccionamos el codigo y veremos el mensaje Francis:“Me gustan los cisnes”, lo que nos da una pista del nombre de un usuario y de su contrasena.

Nos conectamos via SSH con las credenciales obtenidas.

ssh Francis@(IP)

Desde la cuenta de Francis, verificamos los permisos de sudo. 

sudo -l 

Observamos que Francis puede escalar privilegios a Darren por el binario /env

/usr/local/bin/env

Miramos en el Desktop de Darren y encontramos un file user.txt con este mensaje: recordatorio: mirar el email que me ha mandado Jefe con mis credenciales para poder acceder a mysql.

Nos ponemos a la escucha con wireshark y encontramos el email de Jefe con las credenciales de Mysql de Darren.

Accedemos a Mysql con las credenciales de Darren

mysql -u Darren -p

USE user_data; SELECT * FROM user_info;

Encontramos las credenciales de Jefe

Accedemos por ssh con las credenciales de Jefe encontradas

ssh Jefe@(IP)

Buscamos y en Documentos encontramos el Flag.txt
