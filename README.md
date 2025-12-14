# FTP - Configuración, cliente gráfico, instalación de un servidor vsftpd y acceso seguro

## Paso 1. Configuración de partida y DNS

En el archivo `irene.test.dns` he añadido los siguientes registros:

`ns.irene.test. IN A 192.168.56.101` para asociar el nombre de dominio `ns.irene.test` a la dirección IP del servidor DNS.

`ftp.irene.test. IN CNAME ns.irene.test.` para asignar al nombre de dominio `ftp.irene.test` un alias que apunte a la misma IP que el servidor DNS.

He modificado la IP de la máquina virtual de `192.168.56.10` a la propuesta en el enunciado de la práctica, que sería `192.168.56.101`. 
Además, he modificado sus respectivas referencias dentro de los archivos del servidor DNS.

También he modificado la configuración de mi máquina anfitriona para que el DNS primario apunte a la dirección IP de la máquina virtual, que es donde se aloja el servidor DNS. 
Comprobé que este cambio fuera válido utilizando la herramienta `nslookup` seguido del nombre de dominio y la IP del servidor DNS.

## Paso 2. Cliente FTP Gráfico

Para esta parte, he decidido instalar el cliente WinSCP, y para comprobar que todo funcionaba bien establecí una conexión anónima `anonymous`, sin contraseña, e intenté bajarme y subir un archivo.

![Intento de bajada](img/intento-bajada.jpeg)

![Intento de subida](img/intento-subida.jpeg)

Como es anónimo, solo puede bajarse archivos.
