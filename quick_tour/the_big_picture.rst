Un primer vistazo
=================

Iniciaremos usando Symfony2 en solo 10 minutos ! Este capítulo abarca algunos
de los conceptos más importantes sobre los que esta construido Symfony2. 
Se explicará como iniciar de forma rápida a través de un sencillo proyecto de ejemplo. 

Si usted ya ha usado un framework para desarrollo web, seguramente se sentirá
como en casa con Symfony2. Si no es su caso, bienvenido a una nueva forma 
para desarrollar aplicaciones web!

.. tip:

    Quiere aprender por qué y cuando usar un framework ? Lea el documento
    "`Symfony en 5 minutos`"

Descarga de Symfony2
----------------------------------

Primero que todo, verfique que tiene instalado y configurado un servidor
web (como Apache) con PHP 5.3.2 o superior. 

Listo ? Empezaremos por descargar "`la edición estándar de Symfony2`". Para facilitar las cosas aún
más, usaremos el "Symfony2 sandbox". Este es un proyecto Symfony2 preconfigurado
que incluye algunos controladores con sus respectivas librerías. La gran ventaja
que tiene el sandbox sobre otros métodos de instalación es que se puede empezar
a experimentar con Symfony2 inmediatamente. 

Una vez descargue el `sandbox`_, extraigalo en el directorio raíz de su
servidor web. Ahora debería tener un directorio `sandbox/` :: 

    www/ <- directorio raíz del servidor web
        Symfony/ <- directorio que se extrajo
            app/
                cache/
                config/
                logs/
            src/
                Sensio/
                    HelloBundle/
                        Controller/
                        Resources/
            vendor/
                symfony/
                doctrine/
                ...
            web/

.. index::
   single: Installation; Check

Verificando la configuración
----------------------------

Symfony2 integra una interfaz visual para verificar la configuración del 
servidor que es muy útil para solucionar problemas relacionados con la
configuración del servidor web o de PHP. Use la siguiente url para 
observar el diagnóstico: 

    http://localhost/sandbox/web/check.php

Revise atentamente el resultado y realice las correcciones pertinentes. 

Ahora puede ejecutar su primera aplicación "real" Symfony2:  

    http://localhost/sandbox/web/app_dev.php/

Symfony2 debería felicitarle por su arduo trabajo hasta el momento ! 

Creando su primera aplicación
-----------------------------

El sandbox viene con una sencilla ":term:`applicación`" Hello World que
usaremos para aprender más sobre Symfony2. Vaya a la siguiente URL para que
Symfony2 le dé la bienvenida (reemplace Fabien por su primer nombre):  

    http://localhost/sandbox/web/app_dev.php/hello/Fabien

Qué sucedió ? Bien, revisemos la url por partes: 

.. index:: Front Controller

* ``app_dev.php``: Este es el "controlador frontal". Es el único punto
  de entrada de la aplicación y el cual responde todos las solicitudes. 

* ``/hello/Fabien``: Esta es la ruta virtual para el recurso que el el usuario 
  quiere acceder.

La responsabiliad del desarrollador es escribir el código que mapea la
petición del usuario (``/hello/Fabien``) a el recurso asociado con ésta. (``Hello
Fabien!``). 

.. index::
   single: Configuration

Configuración
~~~~~~~~~~~~~

Los archivos de configuración de Symfony2 puede ser escritos en PHP, XML o
`YAML`_. Los diferentes tipos son compatibles entre sí y pueden ser usados
entre aplicaciones. 

.. tip::

    El sandbox usa por defecto el formato YAML, pero esto puede ser cambiado
    fácilmente a XML o PHP editando el archivo ``app/AppKernel.php`` y
    modificando el método ``registerContainerConfiguration``. 
    
.. index::
   single: Routing
   pair: Configuration; Routing

Enrutamiento
~~~~~~~~~~~~

Symfony2 direcciona las peticiones a su código usando un archivo de configuración. Aquí
hay unos ejemplos del archivo de configuración de enrutamiento para nuestras
aplicaciones:  

.. configuration-block::

    .. code-block:: yaml

        # app/config/routing.yml
        homepage:
            pattern:  /
            defaults: { _controller: FrameworkBundle:Default:index }

        hello:
            resource: "@HelloBundle/Resources/config/routing.yml"

    .. code-block:: xml

        <!-- app/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="homepage" pattern="/">
                <default key="_controller">FrameworkBundle:Default:index</default>
            </route>

            <import resource="@HelloBundle/Resources/config/routing.xml" />
        </routes>

    .. code-block:: php

        // app/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('homepage', new Route('/', array(
            '_controller' => 'FrameworkBundle:Default:index',
        )));
        $collection->addCollection($loader->import("@HelloBundle/Resources/config/routing.php"));

        return $collection;

Las primeras líneas del archivo de configuración de enrutamiento definen
el código que es ejecutado cuando el usuario hace una petición al recurso
especificado por el patrón "``/``" (i.e. the homepage). Aquí, se ejecutará
el método ``index`` del controlador ``Default`` dentro de ``FrameworkBundle``. 



Take a look at the last directive of the configuration file: Symfony2 can
include routing information from other routing configuration files by using
the ``import`` directive. In this case, we want to import the routing configuration
from ``HelloBundle``. A bundle is like a plugin that has added power and
we'll talk more about them later. For now, let's look at the routing configuration
that we've imported:

.. configuration-block::

    .. code-block:: yaml

        # src/Sensio/HelloBundle/Resources/config/routing.yml
        hello:
            pattern:  /hello/{name}
            defaults: { _controller: HelloBundle:Hello:index }

    .. code-block:: xml

        <!-- src/Sensio/HelloBundle/Resources/config/routing.xml -->
        <?xml version="1.0" encoding="UTF-8" ?>

        <routes xmlns="http://www.symfony-project.org/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.symfony-project.org/schema/routing http://www.symfony-project.org/schema/routing/routing-1.0.xsd">

            <route id="hello" pattern="/hello/{name}">
                <default key="_controller">HelloBundle:Hello:index</default>
            </route>
        </routes>

    .. code-block:: php

        // src/Sensio/HelloBundle/Resources/config/routing.php
        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('hello', new Route('/hello/{name}', array(
            '_controller' => 'HelloBundle:Hello:index',
        )));

        return $collection;

As you can see, the "``/hello/{name}``" resource pattern is mapped to a controller,
referenced by the ``_controller`` value. The string enclosed in curly brackets
(``{name}``) is a placeholder and defines an argument that will be available
in the controller.

.. index::
   single: Controller
   single: MVC; Controller

Controllers
~~~~~~~~~~~

The controller defines actions to handle users requests and prepares responses
(often in HTML).

.. code-block:: php
   :linenos:

    // src/Sensio/HelloBundle/Controller/HelloController.php

    namespace Sensio\HelloBundle\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class HelloController extends Controller
    {
        public function indexAction($name)
        {
            return $this->render('HelloBundle:Hello:index.html.twig', array('name' => $name));

            // render a PHP template instead
            // return $this->render('HelloBundle:Hello:index.html.php', array('name' => $name));
        }
    }

The code is pretty straightforward but let's explain it line by line:

* *line 3*: Symfony2 takes advantage of new PHP 5.3 namespacing features,
  and all controllers should be properly namespaced. As you can see, the
  namespace has a correlation to the actual file location. In this example,
  the controller lives in the bundle named ``HelloBundle``, which forms the
  first part of the ``_controller`` routing value.


* *line 7*: The controller name is the combination of the second part of the
  ``_controller`` routing value  (``Hello``) and the word ``Controller``. It
  extends the built-in ``Controller`` class, which provides useful shortcuts
  (as we will see later in this tutorial). The ``Controller`` resides in
  ``Symfony\Bundle\FrameworkBundle\Controller\Controller`` which we defined
  on line 5.

* *line 9*: Each controller consists of several actions. As per the routing
  configuration, the hello page is handled by the ``index`` action (the third
  part of the ``_controller`` routing value). This method receives the
  placeholder values as arguments (``$name`` in our case).

* *line 11*: The ``render()`` method loads and renders a template file
  (``HelloBundle:Hello:index.html.twig``) with the variables passed as a
  second argument. In our example, this corresponds to the file
  ``src\Sensio\HelloBundle\Resources\views\Hello\index.html.twig``.

Bundles
~~~~~~~

But what is a :term:`bundle`? All the code you write in a Symfony2 project is
organized in bundles. In Symfony2 speak, a bundle is a structured set of files
(PHP files, stylesheets, JavaScripts, images, ...) that implements a single
feature (a blog, a forum, ...) and which can be easily shared with other
developers. In our example, we only have one bundle, ``HelloBundle``.

Templates
~~~~~~~~~

The controller renders the ``HelloBundle:Hello:index.html.twig`` template. By
default, the sandbox uses Twig as its template engine but you can also use
traditional PHP templates if you choose.

.. code-block:: jinja

    {# src/Sensio/HelloBundle/Resources/views/Hello/index.html.twig #}
    {% extends "HelloBundle::layout.html.twig" %}

    {% block content %}
        Hello {{ name }}!
    {% endblock %}

Congratulations! You've had your first taste of Symfony2 code and created
your first page. That wasn't so hard, was it? There's a lot more to explore,
but you should already see how Symfony2 makes it really easy to implement
web sites better and faster.

.. index::
   single: Environment
   single: Configuration; Environment

Working with Environments
-------------------------

Now that you have a better understanding of how Symfony2 works, have a closer
look at the bottom of the page; you will notice a small bar with the Symfony2
and PHP logos. This is called the "Web Debug Toolbar" and it is the developer's
best friend. Of course, such a tool must not be displayed when you deploy your
application to production. That's why you will find another front controller in
the ``web/`` directory (``app.php``), optimized for the production environment:

    http://localhost/sandbox/web/app.php/hello/Fabien

And if you use Apache with ``mod_rewrite`` enabled, you can even omit the
``app.php`` part of the URL:

    http://localhost/sandbox/web/hello/Fabien

Last but not least, on the production servers, you should point your web root
directory to the ``web/`` directory to secure your installation and have an even
better looking URL:

    http://localhost/hello/Fabien

To make the production environment as fast as possible, Symfony2 maintains a
cache under the ``app/cache/`` directory. When you make changes to the code or
configuration, you need to manually remove the cached files. When developing
your application, you should use the development front controller (``app_dev.php``),
which does not use the cache. When using the development front controller,
your changes will appear immediately.

Final Thoughts
--------------

Thanks for trying out Symfony2! By now, you should be able to create your own
simple routes, controllers and templates. As an exercise, try to build
something more useful than the Hello application! If you are eager to
learn more about Symfony2, dive into the next section: "The View".

.. _sandbox: http://symfony-reloaded.org/code#sandbox
.. _YAML:    http://www.yaml.org/
