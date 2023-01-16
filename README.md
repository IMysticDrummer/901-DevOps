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
