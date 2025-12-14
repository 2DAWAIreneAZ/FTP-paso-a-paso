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

![Intento de bajada](img/intento-bajada.png)

![Intento de subida](img/intento-subida.jpeg)

Como es anónimo, solo puede bajarse archivos.

## Paso 3. Instalación y configuración vsftpd

Primero instalamos el servidor vsftpd utilizando el gestor de paquetes del sistema.

![Instalo vsftpd](img/download-vsftpd.jpeg)

Una vez instalado, se comprueba que el usuario `ftp` ha sido creado automáticamente y que su directorio es `/srv/ftp`.

![Compruebo usuario ftp](img/usuario-ftp.jpeg)

Tras esto, compruebo a qué grupo está asociado.

![Grupo asociado al user ftp](img/grupo-asociado.jpeg)

Verifico la existencia del directorio `/srv/ftp` y que sus permisos sean correctos.

![Permisos del directorio ftp](img/permisos-ftp.jpeg)

Para conocer qué usuarios no pueden acceder al servicio FTP, muestro la lista del archivo `/etc/ftpusers`.

![Muestro contenido ftpusers](img/contenido-ftpusers.jpeg)

Compruebo que el servicio vsftpd está iniciado y en ejecución.

![Estado vsftpd](img/vsftpd-status.jpeg)

Compruebo que está escuchando el puerto 21, siendo el estándar para este servicio.

![Puerto correcto](img/correct-port.jpeg)

Por si ocurre cualquier cosa, se hace una copia de seguridad del archivo de configuración principal.

![Copia de seguridad](img/copia-seguridad.jpeg)

A continuación, se crean los usuarios locales que podrán utilizar el servicio FTP.

![Creación usuarios locales](img/ftp-users.jpeg)

Creo los ficheros de prueba que posteriormente utilizaré.

![Creación de ficheros que luego se usarán](img/creacion-ficheros.jpeg)

Se modifica el archivo de configuración según los requisitos indicados, aunque antes hay que eliminar los comentarios originales y los sustituyo por comentarios propios para explicar cada directiva:

![Standalone e IPv6 fuera](img/standalone.jpeg)

![Mensaje de bienvenida](img/mensaje-bienvenida.jpeg)

![Para usuarios anonimos](img/usuarios-anonimos.jpeg)

El mensaje definido con `ftpd_banner` es una variable global y solo puede contener un único mensaje. Si se usara para mostrar también el mensaje de los usuarios anónimos, se sobrescribiría al mensaje general y se mostraría a todos los usuarios. Por ello, he utilizado un archivo .message para mostrar un mensaje específico únicamente a los usuarios anónimos, manteniendo separado el mensaje general del servidor.

![Mensaje usuarios anonimos](img/mensaje-anonimos.jpeg)

![Tiempo de espera](img/tiempo-espera.jpeg)

![Control del ancho de banda](img/control-ancho-banda.jpeg)

![Control anonimos](img/control-anonimos.jpeg)

![Control locales](img/control-locales.jpeg)

![Enjaulamiento](img/enjaulamiento.jpeg)

![No enjaulada](img/maria-noenjaulada.jpeg)

![Control locales](img/control-locales.jpeg)

En el archivo `vsftpd.chroot_list`, añado a los usuarios que van a ser libres, es decir, que van a poder moverse entre carpetas y que no van a estar únicamente en `/home/<name>`. En este caso, solo tengo que añadir al usuario `maria`.

![Lista no enjaulados](img/lista-noenjaulados.jpeg)

Tras aplicar los anteriores cambios, reinicio el servidor FTP, compruebo que está activo y que sigue escuchando por el puerto 21/TCP.

![Reinicio y estado](img/reinicio-estado-puerto.jpeg)

Al comprobar que todo está en order, realizo una prueba con cada usuario distinto para asegurarme:

Usuario `anonymous`

![Entro en anonimo](img/anonimo-prueba-secuencia.jpeg)

Usuario `maria`

![Vista gráfica](img/vista-grafica.jpeg)

![Vista secuencia](img/vista-secuencia.jpeg)

Usuario `luis`

![Vista grafica luis](img/vista-grafica-luis.jpeg)

![Vista secuencia luis](img/vista-secuencia-luis.jpeg)

## Paso 4. Servidor vsftpd seguro

En este apartado, se configura el servicio FTP para permitir conexiones seguras mediante **FTPS** utilizando un certificado de servidor. Además, se restringe el uso de conexiones cifradas a anónimos.

Primero, se genera un certificado SSL/TLS que será utilizado por el servidor FTP con el siguiente comando:
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/certs/irene.test.pem -out /etc/ssl/certs/irene.test.pem
```

Permisos que necesita:
```
sudo chmod 600 /etc/ssl/certs/irene.test.pem
sudo chown root:root /etc/ssl/certs/irene.test.pem
```

Se modifica el archivo `/etc/vdftpd.conf` para habilitar las conexiones seguras mediante SSL/TLS. Tras esto, se reinicia el servidor, se comprueba que esté activo el servicio y que sigue escuchando por el puerto 21.

![Habilita conexiones seguras](img/conexiones-seguras.jpeg)

![Reinicio + servicio](img/reinicio-servicio.jpeg)

![Puerto 21](img/puerto-21.jpeg)

Se comprueba desde el usuario de `luis` si se ha autenticado bien:

![Inicio de sesion](img/luis-login.jpeg)

![Mensaje de advertencia](img/message-login.jpeg)

![Candado que muestra que es seguro](img/candado-user.jpeg)

![Me descargo un archivo](img/descarga-archivo-luis.jpeg)

Por otro lado, si lo hacemos con `anonymous`:

![Falla con anónimo](img/error-anonymous.jpeg)

Y por último con `maria`:

![Acceso seguro maria](img/maria-login.jpeg)










