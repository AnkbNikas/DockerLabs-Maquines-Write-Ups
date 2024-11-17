Maquina: Hedgehog

Autor: AnkbNikas

Dificultad: Muy Facil

![image](https://github.com/user-attachments/assets/f6bd58de-24c1-4e90-a49c-013163d99fa6)

1.Reconocimiento:

Escaneo de la red
Siempre como primer paso revisamos que haya conexión con la maquina (objetivo), 
para esto lanzamos un simple ping a la ip objetivo que en este caso al correr la maquina nos ahorra el trabajo
de localizar el host especifico que en caso de no encontrarlo podemos usar nmap -sn y escanear toda la red.

ping 172.17.0.2 -c 1

![Ping](https://github.com/user-attachments/assets/fdd0621d-7d57-46b0-b0e2-783916f13f53)

Luego, identificamos los servicios en los puertos abiertos:

sudo nmap -p- -sCV 172.17.0.2 -Pn -n --min-rate 5000

Encontramos los puertos 22 & 80 abiertos

![Nmap](https://github.com/user-attachments/assets/0b2c836a-c2a3-4ed5-a28d-9e8902a75c23)

Abrimos el navegador y navegamos a (IP). En la página principal, veremos el mensaje “tails”, 
lo que nos da una pista del nombre de usuario.

![URL](https://github.com/user-attachments/assets/f8d0c398-0d3b-4523-ba25-aa041fe057b9)

2.Explotación
Usamos una herramienta de fuerza bruta para obtener acceso al usuario tails utilizando rockyou invertido.
sudo hydra -l tails -P invertido.txt ssh://(IP)

![Captura de pantalla 2024-11-17 170210](https://github.com/user-attachments/assets/0c45b4b4-2a83-44e5-b3cc-1488d57aed47)

![Captura de pantalla 2024-11-17 170218](https://github.com/user-attachments/assets/bc88ab97-a730-4238-8e77-558fbade388c)

![Captura de pantalla 2024-11-17 170231](https://github.com/user-attachments/assets/99e8a652-e97c-4ba6-84a5-fe3d3fa2b08d)

Nos conectamos via SSH con las credenciales obtenidas.
ssh tails@(IP)

![SSH](https://github.com/user-attachments/assets/57de162e-0429-4269-bc59-b53d854bbdbc)

3.Escalada de privilegios

Vamos a home para buscar mas usuarios y encontramos sonic.

cd ..

ls

Desde la cuenta de tails, verificamos los permisos de sudo.

sudo -l

Observamos que tails puede ejecutar comandos como sonic sin contraseña.

Cambiamos a la cuenta de sonic.

sudo -u sonic -s

![Escalada](https://github.com/user-attachments/assets/74ae9eaf-a6a9-48eb-b82e-d945d09fd511)

Verificamos los permisos de sudo nuevamente.

sudo -l

Observamos que sonic puede ejecutar cualquier comando como root sin contraseña.

Finalmente, escalamos privilegios a root.

sudo -i

![Root](https://github.com/user-attachments/assets/fbe14574-dacd-4a48-b895-4d01333f93f1)
