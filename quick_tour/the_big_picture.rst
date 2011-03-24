Un primer vistazo
=================

Iniciaremos usando Symfony2 en tan solo 10 minutos !. Este capítulo 
abarca algunos de los conceptos más importantes sobre los que esta 
construido Symfony2. Explicaremos como iniciar de forma rápida a través 
de un sencillo proyecto de ejemplo. 

Si ya has usado un framework para desarrollo web, seguramente te 
sentirás a gusto con Symfony2. Si no es tu caso, bienvenido a una nueva 
forma de desarrollar aplicaciones web!

.. tip:

    Quieres aprender por qué y cuando usar un framework ? Lee el documento
    "`Symfony en 5 minutos`_"

Descarga de Symfony2
----------------------------------

Primero que todo, verfica que tienes instalado y configurado un servidor
web (como Apache) con PHP 5.3.2 o superior. 

Listo ? Empecemos por descargar la "`edición estándar de Symfony2`_", una
:term:`distribución` de Symfony que se encuentra preconfigurada para la mayoría
de casos y que también contiene código de ejemplo que demuestra cómo usar 
Symfony2 (puedes obtener un paquete con incluya los *vendors* para empezar 
aún más rápido).  

Después de extraer el paquete bajo el directorio raíz de su servidor 
web, deberías tener un directorio ``Symfony`` con una estructura como
se muestra a continuación: 

.. code-block:: text

    www/ <- directorio raíz del servidor web
        Symfony/ <- directorio que se extrajo
            app/
                cache/
                config/
                logs/
            src/
                Acme/
                    DemoBundle/
                        Controller/
                        Resources/
            vendor/
                symfony/
                doctrine/
                ...
            web/
                app.php

Verificando la configuración
----------------------------

Symfony2 integra una interfaz visual para probar la configuración del servidor 
muy útil para solucionar problemas relacionados con el servidor web o 
por una incorrecta configuración de PHP. Usa la siguiente url para 
observar el diagnóstico: 

.. code-block:: text

    http://localhost/Symfony/web/config.php

Si se listan errores o aspectos de configuración pendientes, corrígelos;
puedes realizar los ajustes siguiendo las recomendaciones. Cuando todo
se encuentre listo, haz click en el enlace: "Go to the Welcome page"
para ver tu primera aplicación "real" en Symfony2: 

.. code-block:: text

    http://localhost/Symfony/web/app_dev.php/

Symfony2 debería felicitarte por tú arduo trabajo hasta el momento ! 


Entiendo los fundamentos
------------------------

Uno de los principales objetivos de un framework es garantizar la 
`separación de responsabilidades`_. Mantiene tu código organizado y le
permite a tu aplicación crecer fácilmente en el tiempo evitando la mezcla
de llamados a bases de datos, etiquetas HTML y código de lógica de negocio
en un mismo archivo. Para lograr este objetivo, debes aprender algunos
conceptos y términos fundamentales. 

.. tip::
    
    Quieres más pruebas de que usar un framework es mucho mejor que mezclar
    todo en un mismo archivo ? Lee el capítulo "`De PHP plano a Symfony2`_"
    del libro.

La distribución viene con algunos ejemplos de código que usarás para aprender
más sobre los principales conceptos de Symfony2. Ingresa a la siguiente 
URL para ser saludado por Symfony2 (reemplaza *Fabien* por tu primer nombre):

.. code-block:: text

    http://localhost/Symfony/web/app_dev.php/demo/hello/Fabien

Qué sucedió ? Bien, revisemos la url por partes: 

* ``app_dev.php``: Este es el :term:`controlador frontal`. Es el único punto
  de entrada de la aplicación y el cual responde todos las solicitudes. 

* ``/demo/hello/Fabien``: Esta es la *ruta virtual* del recurso que el usuario 
  quiere acceder.

Tú responsabiliad como desarrollador es escribir el código que mapea la
petición del usuario (``/demo/hello/Fabien``) a el recurso asociado con ésta 
(``Hello Fabien!``). 

Enrutamiento
~~~~~~~~~~~~

Symfony2 direcciona las peticiones al código que las gestiona tratando de
encontrar correspondencia entre la URL solicitada y algunos patrones de
configuración. Por omisión, estos patrones están definidos en el 
archivo de configuración ``app/config/routing.yml``: 

.. code-block:: yaml

    # app/config/routing.yml
    _welcome:
        pattern:  /
        defaults: { _controller: AcmeDemoBundle:Welcome:index }

    _demo:
        resource: "@AcmeDemoBundle/Controller/DemoController.php"
        type:     annotation
        prefix:   /demo

Las primeras tres líneas del archivo de configuración de enrutamiento definen
el código que es ejecutado cuando el usuario hace una petición al recurso
especificado por el patrón "``/``" (i.e. la página inicial). Cuando este
recurso es solicitado, el controlador  ``AcmeDemoBundle:Welcome:index`` 
será ejecutado. 

.. tip::

    La edición estándar de Symfony2 usa el formato `YAML`_ para sus
    archivos de configuración, sin embargo Symfony2 también soporta XML,
    PHP y anotaciones de forma nativa. Los diferentes formatos son 
    compatibles y pueden ser usados indistintamente dentro de una aplicación.
    De igual manera, el rendimiento de tu aplicación no depende del formato
    de configuración que escojas ya que todo se almacena en cache en la
    primera petición. 

Controladores
~~~~~~~~~~~~~

Un controlador gestiona las *peticiones* entrantes y retorna *respuestas*
(la mayoría de veces en HTML). A cambio de usar las variables globales
de PHP y funciones para manejar estos mensajes HTTP, Symfony usa objetos:
:class:`Symfony\\Component\\HttpFoundation\\Request` y 
:class:`Symfony\\Component\\HttpFoundation\\Response`. El controlador más
simple posible crea una respuesta, basado en la petición::

    use Symfony\Component\HttpFoundation\Response;

    $name = $request->query->get('name');

    return new Response('Hello '.$name, 200, array('Content-Type' => 'text/plain'));

.. note::

    No te dejes engañar por la simpleza de los conceptos y el poder que 
    encierran. Lee el capítulo "`La especificación HTTP y Symfony2`_" del
    libro para conocer más sobre cómo Symfony2 aprovecha el protocolo 
    HTTP y por qué éste hace las cosas más simples y más poderosas al 
    mismo tiempo. 

Symfony2 elige el controlador basado en el valor del parámetro ``_controller``
del archivo de configuración de enrutamiento: ``AcmeDemoBundle:Welcome:index``.
Esta cadena de texto es el *nombre lógico* del controlador y hace referencia
al método ``indexAction`` de la clase 
``Acme\DemoBundle\Controller\WelcomeController``::

    // src/Acme/DemoBundle/Controller/WelcomeController.php
    namespace Acme\DemoBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class WelcomeController extends Controller
    {
        public function indexAction()
        {
            return $this->render('AcmeDemoBundle:Welcome:index.html.twig');
        }
    }

.. tip::

    También habrías podido usar
    ``Acme\DemoBundle\Controller\WelcomeController::indexAction`` como valor
    del parámetro ``_controller``, pero si sigues algunas convenciones
    sencillas, el nombre lógico es más conciso y permite mayor flexibilidad. 

La clase controlador extiende de la clase ``Controller`` (incluída en el framework),
la cual provee métodos muy útiles, como el método
:method:`Symfony\\Bundle\\FrameworkBundle\\Controller\\Controller::render`
que carga y muestra una plantilla (``AcmeDemoBundle:Welcome:index.html.twig``).
El valor retornado es un objeto Response que almacena el contenido 
renderizado. De esta manera, en caso de ser necesario, la respuesta (el
objeto Response) puede ser adaptada antes de ser enviada al navegador::

    public function indexAction()
    {
        $response = $this->render('AcmeDemoBundle:Welcome:index.txt.twig');
        $response->headers->set('Content-Type', 'text/plain');

        return $response;
    }

.. tip::

    Extender de la clase base ``Controller``es opcional. De hecho, un
    controlador puede ser una función PHP o aún una función anónima
    (conocidas también como closures). El capítulo "`El Controlador`_"
    del libro te enseñará todo sobre los controladores en Symfony2. 

El nombre de la plantilla, ``AcmeDemoBundle:Welcome:index.html.twig``, 
es el *nombre lógico* de la plantilla  y hace referencia al archivo 
``src/Acme/DemoBundle/Resources/views/Welcome/index.html.twig``. De nuevo, 
más adelante en la sección bundles se explicará  la utilidad de estos 
nombres.  

Ahora, revisemos las últimas líneas del archivo de configuración de 
enrutamiento: 

.. code-block:: yaml

    # app/config/routing.yml
    _demo:
        resource: "@AcmeDemoBundle/Controller/DemoController.php"
        type:     annotation
        prefix:   /demo

Symfony2 puede leer la información de enrutamiento desde diferentes
recursos escritos en YAML, XML, PHP o embebidos en anotaciones PHP. Aquí,
el *nombre lógico* del recurso es ``@AcmeDemoBundle/Controller/DemoController.php`` 
y hace referencia al archivo src/Acme/DemoBundle/Controller/DemoController.php``. 
En este archivo, las rutas están definidas como anotaciones sobre los métodos
que involucran acciones::

    // src/Acme/DemoBundle/Controller/DemoController.php
    class DemoController extends Controller
    {
        /**
         * @extra:Route("/hello/{name}", name="_demo_hello")
         * @extra:Template()
         */
        public function helloAction($name)
        {
            return array('name' => $name);
        }

        // ...
    }

La anotación ``@extra:Route()`` define la ruta para el método ``helloAction`` 
y el patrón que es ``/hello/{name}``. La cadena encerrada entre llaves ``{name}`` 
es un marcador de posición (conocido como placeholder). Como puedes ver, su valor
puede ser recuperado a través del argumento ``$name`` del método. 

.. note::
    
    Aún cuando las anotaciones no están nativamente soportadas por PHP, 
    su uso es extensivo en Symfony2 como una forma muy conveniente de
    configurar el comportamiento del framework y conservar la configuración
    junto al código. 

Si das un vistazo más de cerca al código de la acción, podrás ver que 
a cambio de ejecutar una plantilla como antes, solo se retorna un arreglo
de parámetros. La anotación ``@extra:Template()`` se encarga de ejecutar
una plantilla cuyo nombre es determinado con base en algunas sencillas
convenciones (ejecutará la plantilla ``src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig``)


.. tip::
    
    Las anotaciones ``@extra:Route()`` y ``@extra:Template()`` son mucho
    más poderosas de lo que se ha mostrado en este tutorial. Puedes aprender
    más sobre "`anotaciones en controladores`_" en la documentación oficial. 


Plantillas
~~~~~~~~~~

El controlador ejecuta la plantilla 
``src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig`` (o 
``AcmeDemoBundle:Demo:hello.html.twig`` si tu usas el nombre lógico): 

.. code-block:: jinja

    {# src/Acme/DemoBundle/Resources/views/Demo/hello.html.twig #}
    {% extends "AcmeDemoBundle::layout.html.twig" %}

    {% block title "Hello " ~ name %}

    {% block content %}
        <h1>Hello {{ name }}!</h1>
    {% endblock %}

Por omisión, Symfony2 usa `Twig`_ como su motor de plantillas pero tú puedes
usar plantillas PHP tradicionales si así lo prefieres. El siguiente capítulo
te enseñará como trabajan las plantillas en Symfony2. 

Bundles
~~~~~~~

Puede que te hayas preguntado por qué la palabra :term:`bundle` es usada
en muchos de los nombres que hemos visto hasta ahora. Todo el código que 
escribas para tu aplicación es organizado en bundles. En el vocabulario
de Symfony2, un bundle es un grupo estructurado de archivos (archivos PHP, 
hojas de estilo, JavaScripts, imágenes, ...) que implementan una única
funcionalidad (un blog, un foro, ...) y la cual puede ser fácilmente
compartida con otros desarrolladores. Hasta el momento, hemos manipulado
un bundle, ``AcmeDemoBundle``. Aprenderás más sobre bundles en el último
capítulo de este tutorial. 

Trabajando con entornos
-----------------------

Ahora que ya tienes un mejor conocimiento sobre como trabaja Symfony2, dá 
un vistazo a la parte inferior de la página; te darás cuenta de una pequeña
barra con el logo de Symfony2. Es la barra de depuración web, la mejor
amiga del desarrollador. Pero solo es la punta del iceberg; haz click 
sobre el raro número hexadecimal para revelar otra útil herramienta de
depuración de Symfony2: El profiler.  

Desde luego, estas herramientas no deben estar disponibles cuando despliegues
tu aplicación en producción. Es por eso que encontrarás otro controlador
frontal in el directorio ``web/`` (``app.php``), optimizado para el 
entorno de producción: 

.. code-block:: text

    http://localhost/Symfony/web/app.php/demo/hello/Fabien

Y si estás usando Apache con ``mod_rewrite`` habilitado, puedes omitir 
el uso de ``app.php`` en la URL: 

.. code-block:: text

    http://localhost/Symfony/web/demo/hello/Fabien

Como última cosa pero no menos importante, en los servidores de producción,
deberías apuntar el directorio raíz del servidor web al directorio ``web/``
para asegurar tu instalación y tener un URL más amigable: 

.. code-block:: text

    http://localhost/demo/hello/Fabien

Para hacer que tu aplicación responda de forma rápida, Symfony2 mantiene
una cache bajo el directorio ``app/cache``. En el entorno de desarrollo 
(``app_dev.php``), esta cache es vaciada automáticamente cuando
hagas cambios al código o la configuración. Pero no será el caso del 
ambiente de producción (``app.php``) para hacer que el rendimiento sea
mejor; este es el porqué tú siempre debes usar el entorno de desarrollo
cuando desarrollas tu aplicación.  

Diferentes :term:`entornos` de una aplicación diferirán solo en su 
configuración. De hecho, una configuración puede heredar de otra: 

.. code-block:: yaml

    # app/config/config_dev.yml
    imports:
        - { resource: config.yml }

    web_profiler:
        toolbar: true
        intercept_redirects: false

El entorno ``dev`` (definido en ``config_dev.yml``) heredará del archivo
global ``config.yml`` y lo extenderá habilitando la barra de depuración
web. 

Conclusión
-----------

Felicitaciones ! Has tenido tu primer contacto con el código de Symfony2. 
No fué tan complicado, verdad ?. Hay mucho por explorar, pero ya deberías
ver cómo Symfony2 hace realmente fácil la implementación de sitios web
mucho mejores y más rápidos. Si estás interesado en aprender más sobre
Symfony2, sumérgete en la siguiente sección: "La vista". 


.. _edición estándar de Symfony2:       http://symfony.com/download
.. _Symfony en 5 minutos:               http://symfony.com/symfony-in-five-minutes
.. _Separación de responsabilidades:    http://en.wikipedia.org/wiki/Separation_of_concerns
.. _De PHP plano a Symfony2:            http://symfony.com/doc/2.0/book/from_flat_php_to_symfony2.html
.. _YAML:                               http://www.yaml.org/
.. _La especificación HTTP y Symfony2:  http://symfony.com/doc/2.0/book/http_fundamentals.html
.. _Learn more about the Routing:       http://symfony.com/doc/2.0/book/routing.html
.. _El Controlador:                     http://symfony.com/doc/2.0/book/controller.html
.. _anotaciones en controladores:       http://bundles.symfony-reloaded.org/frameworkextrabundle/
.. _Twig:                               http://www.twig-project.org/
