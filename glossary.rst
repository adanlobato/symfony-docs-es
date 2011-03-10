:orphan:

Glossary
========

.. glossary::
   :sorted:

   Proyecto
        Un *Proyecto* es un directorio compuesto de una Aplicación, un grupo
        de bundles, bibliotecas vendor, un autoloader, y scripts controladores
        para la intefaz web.

   Aplicación
        Una *Aplicación* es un directorio que contiene la *configuración* de
        un grupo específico de Bundles.

   Bundle
        Un *Bundle* es un grupo estructurado de archivos (archivos PHP, hojas
        de estilo, JavaScripts, imágenes, ...) que *implementan* una única
        característica (un blog, un foro, ...) y la cual pueda ser fácilmente
        compartida con otros desarrolladores.

   Controlador Frontal
        Un *Controlador Frontal* es un pequeño script PHP ubicado en el directorio web
        de su proyecto. Es típico que *todos* los requests sean manejados al ejecutar el
        mismo controlador frontal, cuyo trabajo es arrancar la aplicación en Symfony.

   Servicio
        Un *Servicio* es un término genérico para cualquier objeto PHP que realice una
        tarea específica. Los servicios suelen ser usados "globalmente", como por ejemplo
        un objeto de conexión a una base de datos o uno que envíe correo electrónico.
        En Symfony2, los servicios suelen ser configurados y obtenidos desde el contenedor
        de servicios. Cuando una aplicación tiene muchos servicios que no dependen unos
        de otros se dice que sigue una `arquitectura orientada a servicios`_.

   Contenedor de Servicios
        Un *Contenedor de Servicios*, también conocido como *Contenedor de Inyección de Dependencias*,
        es un objeto especial que administra la instanciación de servicios dentro de
        una aplicación. En lugar de crear los servicios directamente, el desarrollador
        *entrena* al contenedor de servicios (mediante configuración) sobre como crear
        los servicios. El contenedor de servicios se encarga de instanciar e inyectar
        los servicios dependientes según se requiera.

   Especificación HTTP
        La *Especificación Http* es un documento que describe el Protocolo de Transferencia
        de Hipertexto - un grupo de reglas que describen la clásica comunicación cliente
        servidor de petición-respuesta. La especificación define el formado utilizado por
        las peticiones y las respuestas así como las posibles cabeceras HTTP que
        puedan usar. Para más información, lea el artículo de la `Wikipedia sobre Http`_
        o el `RFC HTTP 1.1`_.

.. _`arquitectura orientada a servicios`: http://es.wikipedia.org/wiki/Arquitectura_orientada_a_servicios
.. _`Wikipedia sobre Http`: http://es.wikipedia.org/wiki/HTTP
.. _`RFC HTTP 1.1`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
