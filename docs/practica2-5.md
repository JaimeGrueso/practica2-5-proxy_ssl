# Practica 2.5: Proxy Inverso y Balanceo de Carga con SSL en NGINX

## Indice
1. [Creacion de Certificado Autofirmado](#id1)
2. [Configuración SSL en el Proxy Inverso](#id2)
3. [Comprobaciones](#id3)
4. [Redirección Forzosa a HTTPS](#id4)
5. [Cuestiones Finales](#id5)

<div id='id1'></div>

## Creación de Certificado Autofirmado
Para esta practica vamos a necesitar un certificado SSL autofirmado. Un certificado autofirmado es un certificado de servidor que es firmado por la propia entidad que lo emite. En lugar de confiar en una entidad de certificación (CA) de confianza, los certificados autofirmados son firmados por la misma persona que los emite. Por lo tanto, no son confiables y no se pueden verificar automáticamente. 

En un entorno de producción, siempre debe utilizar un certificado SSL firmado por una CA de confianza. Sin embargo, para fines de desarrollo y pruebas, un certificado autofirmado es una buena opción.

Se creará una carpeta en `/etc/nginx/ssl` donde se almacenarán los certificados y las claves asociadas a ellos (privada/pública). 

Veamos pues el proceso para generar los certificados y las claves asociadas a ellos (privada/pública). En primer lugar debemos crear el siguiente directorio:

`/etc/nginx/ssl`

![Creación de directorio](./assets/images/screenshot.1.jpg)

Una vez creado el directorio, se ejecutará el siguiente comando:

`sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt`

![Generación de certificado](./assets/images/screenshot.2.jpg)

<div id='id2'></div>

## Configuración SSL en el Proxy Inverso
Hecho esto, se procederá a configurar nuestro proxy-inverso. Para ello se deberán de realizar los siguientes pasos:
- Editar el archivo de configuración del proxy-inverso.
- Se deberá de hacer un unlink de este en la carpeta `sites-enabled`.
- Se deberá de agregar la configuración para que acepte los certificados en el `sites-enabled`.

El archivo de configuración deberá de quedar de la siguiente manera:

![Configuración SSL](./assets/images/screenshot.4.jpg)

Se ejecutará el siguiente comando para hacer un unlink del archivo de configuración en la carpeta `sites-enabled`: 

![Configuración SSL](./assets/images/screenshot.5.jpg)

Se ejecutará el siguiente comando para hacer un link del archivo de configuración en la carpeta `sites-enabled`:

![Configuración SSL](./assets/images/screenshot.6.jpg)

<div id='id3'></div>

## Comprobaciones
Las comprobaciones se deberán hacer desde un navegador externo, en el cual accederemos a nuestro ejemplo-proxy.com. Para ello, se deberá tener activa 
una máquina de webserver.

![Comprobación](./assets/images/screenshot.7.jpg)
Una vez dentro, haremos clic en "Aceptar el riesgo y continuar" para proceder con la redirección. Lo importante es verificar si los certificados están correctamente configurados, para ello, haremos clic en el icono del candado ubicado junto a la barra de direcciones de nuestro navegador.

![Comprobación](./assets/images/Captura%20de%20pantalla%202024-11-01%20214522.png)

A continuación, veremos la configuración. Debemos acceder a la sección de ver certificado, donde se mostrará toda la configuración junto con las claves.

![Comprobación](./assets/images/screenshot.9.jpg)

![Comprobación](./assets/images/screenshot.10.jpg)

Como se puede observar, el certificado está correctamente configurado y es válido.

<div id='id4'></div>

## Redirección Forzosa a HTTPS
Para forzar la redirección a HTTPS, se deberá de modificar el archivo de configuración del proxy-inverso. Se deberá de agregar la siguiente configuración:

![Redirección a HTTPS](./assets/images/screenshot.11.jpg)

Una vez hecho esto, se deberá de reiniciar el servicio de nginx.

Para comprobar que la redirección a HTTPS está funcionando correctamente, 
se deberá de acceder a nuestro sitio web y verificar que la redirección 
se ha realizado correctamente.

<div id='id5'></div>

## Cuestiones Finales

### Cuestion 1
Hemos configurado nuestro proxy inverso con todo lo que nos hace falta pero no nos funciona y da un error 
del tipo This site can't provide a secure connection, ERR_SSL_PROTOCOL_ERROR. 

Para resolver el error ERR_SSL_PROTOCOL_ERROR, se asegurará de que el `server_name` coincida con el dominio 
utilizado para acceder al sitio. Se verificará que el certificado y la clave SSL estén en la ubicación correcta 
y tengan los permisos adecuados. Además de TLS 1.3, se utilizará una lista de cifrados más amplia 
como `ssl_ciphers HIGH:!aNULL:!MD5`, y confirma que el servicio en `proxy_pass` sea accesible desde NGINX. 
Después de estos ajustes, se reiniciará NGINX y revisa los logs para identificar posibles problemas adicionales.

### Cuestion 2
Imaginad que intentamos acceder a nuestro sitio web HTTPS y nos encontramos con el siguiente error:

El error `NET::ERR_CERT_REVOKED` significa que el certificado SSL fue revocado y ya no es confiable. 
Para solucionarlo, se obtendrá un nuevo certificado SSL, configurar el servidor para usarlo y reinicia 
el servidor. Luego, verifica que el error se haya solucionado accediendo al sitio.



