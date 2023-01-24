# Configuración y despliegue en servidores

## DevOps

Antes estaban los desarroladores y la gente de sistemas (IT).
Los desarroladores pasaban los programas a IT y estos lo desplegaban en pruebas y los fallos se lo pasaban a de nuevo a la gente de sistemas. Ciclos largos de desarrollo (meses).  
Cuando aparecen las metodologías Agile, el desarrollador hace entregas muy rápidas (2 semanas), y los IT no les daba tiempo ha realizar las pruebas.  
Se busca que tanto desarrolladores como IT hablen el mismo idioma, colaboren, se fomente la comunicación, y la automatización (por ejemplo con procesos de prueba en GIT).

DevOps pretende hacer todo esto de la manera más fácil y rápida posible.

Como desarrolladores tenemos que hacer ese acercamiento.

### Maneras:

- Manera fácil: A través de ser sólo desarrolladores. Esto nos causa una **Deuda Técnica**: es el precio que paga el desarrollador por utilizar plataformas automáticas en la nube.

- Manera Rockstar: Pasar a ser DevOps y ser capaces de preparar y gestionar un servidor que ponga a funcionar nuestros programas. Ser también _administrador de sistemas_.

Ser DevOps, también tenemos que usar, aprender y configurar software creado por otros.

## Ejemplos de infraestructuras (Design Systems - Diseño de sistemas)

https://bytebytego.com - newsletter recomendada.

En el _servidor físico_, instalamos un servidor web. También instalamos un servidor de aplicaciones (por ejemplo node). Instalamos una base de datos (mongo, mysql, mariaDB...), un servidor de correo, etc, etc, etc...

Cuando el servidor físico llega a su límite, lo siguiente para escarlar es utilizar _máquinas dedicadas para cada uno de los apartados anteriores_ (una para la BD, otra para el servidor web y aplicaciones, y otra para el servidor de correo).

Cuando el servidor de aplicaciones y web no da más, lo siguiente es _dedicar varios servidores_, utilizando un _balanceador de carga_.

Cuando la base de datos colapsa, podemos montar un _clúster de BD_.

El siguiente paso para aligerar la carga, es crear un _servidor CDN_ (content delivery network) con archivos estáticos. Los CDN son sólo para servir archivos estáticos de forma ultra rápida.

**Siempre que podamos se deben guardar los archivos estáticos en servidores CDN**. Debemos tener en cuenta que CDN es público. Por tanto si queremos guardar archivos privados, tenemos que tener en cuenta que no podemos guardarlo en formato CDN, sino en apartados (buckets en AWS) privados, y que nuestra aplicación gestione entregar esos archivos sólo a personas autorizadas a acceder a ellos.

El siguiente paso para descargar a nuestros servidores web y de aplicación, es utilizar _colas de tareas_ y un _worker_ que las despache.

Siguiente paso para descargar es utilizar un _servidor de caché (Cache Server)_. Cada vez que se requiera un archivo o datos se almacenan también en la caché, durante un tiempo determinado. Esta caché es mucho más rápido, porque no tiene que buscar en las bases de datos.  
Gestionar una caché es muy complicado. Es necesario utilizar muy buenos nombres, ya que funcionan por pares clave --> valor.

Siguiente paso para descargar es utilizar una _HTTP Cache_. Hace cachés de las peticiones HTTP, de forma que no hace falta volver a hacer toda la petición a los servidores, si ya ha pasado por la HTTP caché y está almacenada y es válida.  
El problema es asegurar las autenticaciones. Se configura las HTTP Caché para que compruebe las cookies de sesión o la cabecera de atenticación, y así asegurar que el contenido que se sirve sea adecuado para el usuario que lo solicita.

Siguiente paso, para liberar el balanceador de carga, es poner varios balanceadores y utilizar un servicio _DNS Round Robin_ que reciba las peticiones y lance a cada uno a un balanceador de carga.

# AWS (Amazon Web Services)

Calculador de costes (a tener en cuenta que no cuesta lo mismo en todas las regiones):  
https://calculator.aws/#/

Servicios gratis de Amazon:  
https://aws.amazon.com/free/

Servicio LightSail son servidores pequeños para gente que está empezando. Es un servicio mucho más simple, pero también es mucho más difícil de conectar con otros servicios.

# LINUX

En AWS para conectarse a una máquina ubuntu, el usuario es _ubuntu_.
`ssh ubuntu@44.203.148.124`  
Para conectarnos usando el certificado:  
`ssh -i archivo-certificado.pem ubuntu@44.203.148.134`  
Para que nos deje conectar tenemos que restringir los permisos del archivo: `chmod 0600`

Subir archivos de una carpeta a través de ssh:  
`scp -r -i archivo-certificado startbootstrap-freelancer-gh-pages ubuntu@44.203.148.124:/home/ubuntu`

Recargar servicios en linux sin parar el servicio:  
`sudo systemctl reload servicio (ssh por ejemplo)`  
Comprobar el estado del servicio:  
`sudo systemctl status servicio (ssh por ejemplo)`

En linux, para dar permisos de acceso a otros usuarios a una carpeta, no sólo hay que dar permisos de lectura, sino que también hay dar **permisos de ejecución a la carpeta**.  
Cuando se añade un usuario a un grupo, si el usuario está conectado, no coge automáticamente los cambios de grupos, hasta que el usuario no salga y vuelva a entrar.

`systemctl` nos va a permitir manejar y comprobar todos aquellos programas que funcionen como procesos del sistema en ejecución.

Comando para reiniciar el servidor/ordenador `sudo reboot`.

Aumentar la información recibida cuando queremos conectarnos, añadimos -v:  
`ssh -v -i archivo-certificado.pem ubuntu@44.203.148.134`

Hacer un enlace directo en linux:  
`(sudo) ln -s archivo-origen-con-ruta-absoluta nombre-acceso-directo`

## NGINX

Comando para comprobar la sintaxis de la configuración de nginx: `sudo nginx -t`

**OJO**  
Cuando subamos aplicaciones REACT, después de hacer un build, hay que tener cuidado con la propiedad _homepage_ del package.json, ya que nos puede causar que busque los archivos en un directorio diferente a dónde estamos.  
Además, cuando la aplicación lleve `react-router`, tenemos que tener en cuenta que el _location_ debemos llamar al index.html, antes del error 404, para que deje el enrutado a REACT.
Antes:

```
server {
    listen 80 default_server;  # escucha peticiones HTTP en el puerto 80 y actua como servidor por defecto
    root /var/www/react-todo;  # busca los archivos de la web en /var/www/react-todo
    index index.html;       # el archivo principal es index.html que estará en la carpeta /var/www/react-todo
    location / {           # cualquier petición que empiece por /
        try_files $uri $uri/ =404;  # intenta servir el archivo que piden en a $uri, si no existe, el de carpeta $uri, y si no, devuelve un 404
    }
}
```

Después:

```
server {
    listen 80 default_server;  # escucha peticiones HTTP en el puerto 80 y actua como servidor por defecto
    root /var/www/react-todo;  # busca los archivos de la web en /var/www/react-todo
    index index.html;       # el archivo principal es index.html que estará en la carpeta /var/www/react-todo
    location / {           # cualquier petición que empiece por /
        try_files $uri $uri/ /index.html;  # intenta servir el archivo que piden en a $uri, si no existe, el de carpeta $uri, y si no, carga el index.html para que react gestione las rutas
    }
}
```

### Usar Nginx como proxi inverso

Se añade otros archivos de configuración, pero **no** indicamos default_server, y configuramos los proxys a las DNS's adecuadas:

```
    server {

        listen 80;
        server_name VUESTRA-DNS-PUBLICA-DE-AMAZON;

        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_redirect off;
        }

    }
```

### Usar Nginx como servidor también de proxy para aplicaciones webshocket

Para que nginx propage la cabecera host, tal cual le llega, a nuestras aplicaciones, debemos añadir tres líneas más.  
Sino, nginx hace un cambio del host a 127.0.0.1 y por tanto puede haber aplicaciones que no funcionen.

```
    server {

        listen 80;
        server_name VUESTRA-DNS-PUBLICA-DE-AMAZON;

        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_redirect off;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
            proxy_set_header Host $host;
        }
    }
```

### Configurar nginx para que sirva los archivos estáticos en vez de nuestra aplicación

Añadir antes de la location principal, otra location que busque y sirva los archivos estáticos.

```
            location ~ ^/(css/ | img/ | js/ | sounds/) {
                root CARPETA-PRINCIPAL-DE-LA-QUE-CUELGAN-LOS-DIRECTORIOS-ESTÁTICOS
        }
    }
```

Lo primero es saber en qué direcciones (carpetas) tenemos nuestros archivos estáticos.

La virgulilla `~` indica que vamos a utilizar una opción regular. `^` indica que la expresión empieza por /, sigue por css, o por img, o por js o por sounds... entonces vete a buscar los archivos a la capreta indicada.

**Asegurar que los permisos de nginx (www-data) esté en el mismo grupo que chat, incorporándole, y así podrá acceder y ejecutar**

### MongoDb autenticacion con SCRAM

**Código a usar**
Desde mongosh, utilizamos este código para crear un usuario administrador:

```
use admin
db.createUser(
  {
    user: "admin",
    pwd: "j6vmUw45rlzXbCef",
    roles: [
      { role: "userAdminAnyDatabase", db: "admin" },
      { role: "readWriteAnyDatabase", db: "admin" }
    ]
  }
)
```

También se puede generar una pwd que nosotros queramos utilizando la función de mongo `passwordPrompt()`.

**Ojo** Mongo nos deja conectarnos, pero no nos deja hacer nada sin habernos conectado... ni siquiera ver las bases de datos.

Con esto hemos creado el usuario administrador de mongodb.
Ahora tenemos que crear usuarios nuevos con sus permisos para trabajar.  
En servidores, cada usuario debe tener su base de datos y usuario para sus trabajos.

1. Creamos una base de datos para el usuario de programa (`use parsedb`).
2. Creamos el usuario para ese programa/base de datos con sus roles asociados:
   1. Usamos el mismo esquema que para el usuario administrador, pero con sus roles, nombres y contraseñas adecuadas:
      ```
          db.createUser(
              {
                  user: "parseusr",
                  pwd: "C0ntr4s3n4",
                  roles: [
                      { role: "readWrite", db: "parsedb" }
                  ]
              }
          )
      ```

Formato de una URL para acceder a un recurso:  
<protocolo>://<usuario>:<contraseña>@<host>:<puerto>/<recurso>  
Usaremos este protocolo para autenticarnos en mongo:  
`mongodb://parseusr:C0ntr4s3n4@localhost:27017/parsedb`  
Esto lo usaremos en las variables de entorno, para poder lanzar la aplicacion sin tocar el index.js.  
**Nunca se cambia el código de lo que está en producción**. Las variables de entorno sirven para dar configuraciones específicas para el despliegue de la aplicación en los servidores.
