La arquitectura
===============

¡Usted es mi héroe! ¿Quién creería que seguiría aquí luego de las tres primeras
partes? Sus esfuerzos serán prontamente recompensados. Las primeras tres partes
revisaron muy vagamente la arquitectura del framework. Ya que esta es lo que
distingue a Symfony2 de otros frameworks, concentrémonos en ella.

Comprensión de la estructura de directorios
-------------------------------------------

La estructura de directorios en una :term:`aplicación` de Symfony2 es bastante
flexible, pero la estructura de directorios de la *Edición Estándar* refleja la
estructura típica y recomendada de una aplicación de Symfony2:

* ``app/``:    La configuración de la aplicación;
* ``src/``:    El código PHP del proyecto;
* ``vendor/``: Las dependencias a terceros;
* ``web/``:    El directorio web raíz.

El directorio web
~~~~~~~~~~~~~~~~~

El directorio web raíz es el hogar de todos los archivo públicos y estáticos
como imágenes, hojas de estilo y archivos JavaScript. También es donde vive cada
:term:`controlador frontal`::

    // web/app.php
    require_once __DIR__.'/../app/bootstrap.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('prod', false);
    $kernel->handle(Request::createFromGlobals())->send();

El kernel requiere primero al archivo ``bootstrap.php``, el cual inicia el
framework y registra al *autoloader* (ver abajo).

Como cualquier controlador frontal, ``app.php`` usa una clase Kernel,
``AppKernel``, para iniciar la aplicación.

El directorio de la aplicación
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

La clase ``AppKernel`` es el punto de ingreso principal a la configuración de la
aplicación y como tal, se ubica en el directorio ``app/``.

Esta clase debe implementar dos métodos:

* ``registerBundles()``: Debe devolver un array de todos los bundles necesarios
  para correr la aplicación;

* ``registerContainerConfiguration()``: Carga la configuración (luego veremos
  más sobre esto);

La auto-carga de PHP puede ser configurada por medio de ``app/autoload.php``::

    // app/autoload.php
    use Symfony\Component\ClassLoader\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'          => array(__DIR__.'/../vendor/symfony/src', __DIR__.'/../vendor/bundles'),
        'Sensio'           => __DIR__.'/../vendor/bundles',
        'JMS'              => __DIR__.'/../vendor/bundles',
        'Doctrine\\Common' => __DIR__.'/../vendor/doctrine-common/lib',
        'Doctrine\\DBAL'   => __DIR__.'/../vendor/doctrine-dbal/lib',
        'Doctrine'         => __DIR__.'/../vendor/doctrine/lib',
        'Zend\\Log'        => __DIR__.'/../vendor/zend-log',
        'Assetic'          => __DIR__.'/../vendor/assetic/src',
        'Acme'             => __DIR__.'/../src',
    ));
    $loader->registerPrefixes(array(
        'Twig_Extensions_' => __DIR__.'/../vendor/twig-extensions/lib',
        'Twig_'            => __DIR__.'/../vendor/twig/lib',
        'Swift_'           => __DIR__.'/../vendor/swiftmailer/lib/classes',
    ));
    $loader->register();

La clase :class:`Symfony\\Component\\ClassLoader\\UniversalClassLoader` se
utiliza para autocargar archivos que respetan ya sea a los `estándares`_ de
inter-operabilidad técnica para *namespaces* de PHP 5.3 o la `convención`_ de
PEAR para nombrar clases. Como puede ver aquí, todas las dependencias están
ubicadas dentro del directorio ``vendor/``, pero esto es solo una convención.
Usted puede ubicarlos donde sea que quiera, en una ubicación global en su
servidor, o en una local en cada proyecto.

Si quiere aprender más acerca de la flexibilidad del *autoloader* de Symfony2,
lea la receta "`Como autocargar clases`_" en el libro de cocina.

Comprensión del sistema de Bundles
----------------------------------

Esta sección introduce una de las más grandiosas y más poderosas características
de Symfony2, el sistema de :term:`bundles`.

Un bundle es algo así como un *plugin* en otros programas. ¿Entonces porqué es
llamado *bundle* y no *plugin*? Por que *todo* es un bundle en Symfony2, desde
los rasgos básicos del framework hasta el código que usted escribe para su
aplicación. Los Bundles son ciudadanos de primera clase en Symfony2. Esto le da
la flexibilidad de usar características pre-construidas empaquetadas en bundles
de terceros o distribuir sus propios bundles. Esto hace fácil coger y
seleccionar que características habilitar en su aplicación y optimizarlas de la
manera que usted quiera.

Registro de un Bundle
~~~~~~~~~~~~~~~~~~~~~

Una aplicación está hecha de bundles tal como está definido en el método
``registerBundles()`` de la clase ``AppKernel``::

    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\SecurityBundle\SecurityBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),
            new Symfony\Bundle\ZendBundle\ZendBundle(),
            new Symfony\Bundle\SwiftmailerBundle\SwiftmailerBundle(),
            new Symfony\Bundle\DoctrineBundle\DoctrineBundle(),
            new Symfony\Bundle\AsseticBundle\AsseticBundle(),
            new Sensio\Bundle\FrameworkExtraBundle\SensioFrameworkExtraBundle(),
            new JMS\SecurityExtraBundle\JMSSecurityExtraBundle(),
            new Acme\DemoBundle\AcmeDemoBundle(),
        );

        if (in_array($this->getEnvironment(), array('dev', 'test'))) {
            $bundles[] = new Symfony\Bundle\WebProfilerBundle\WebProfilerBundle();
            $bundles[] = new Symfony\Bundle\WebConfiguratorBundle\SymfonyWebConfiguratorBundle();
        }

        return $bundles;
    }

Además de ``AcmeDemoBundle`` del que ya hemos hablado, vea que el kernel también
habilita ``FrameworkBundle``, ``DoctrineBundle``, ``SwiftmailerBundle`` y
``AsseticBundle``. Todos ellos son parte del framework base.

Configuración de un Bundle
~~~~~~~~~~~~~~~~~~~~~~~~~~

Cada bundle puede ser adaptado por medio de archivos de configuración escritos
en YAML, XML o PHP. Dele un vistazo a la configuración por omisión:

.. code-block:: yaml

    # app/config/config.yml
    imports:
        - { resource: parameters.ini }
        - { resource: security.yml }

    framework:
        charset:       UTF-8
        error_handler: null
        csrf_protection:
            enabled: true
            secret: %csrf_secret%
        router:        { resource: "%kernel.root_dir%/config/routing.yml" }
        validation:    { enabled: true, annotations: true }
        templating:    { engines: ['twig'] } #assets_version: SomeVersionScheme
        session:
            default_locale: %locale%
            lifetime:       3600
            auto_start:     true

    # Twig Configuration
    twig:
        debug:            %kernel.debug%
        strict_variables: %kernel.debug%

    # Assetic Configuration
    assetic:
        debug:          %kernel.debug%
        use_controller: false

    # Doctrine Configuration
    doctrine:
        dbal:
            default_connection: default
            connections:
                default:
                    driver:   %database_driver%
                    host:     %database_host%
                    dbname:   %database_name%
                    user:     %database_user%
                    password: %database_password%

        orm:
            auto_generate_proxy_classes: %kernel.debug%
            default_entity_manager: default
            entity_managers:
                default:
                    mappings:
                        AcmeDemoBundle: ~

    # Swiftmailer Configuration
    swiftmailer:
        transport: %mailer_transport%
        host:      %mailer_host%
        username:  %mailer_user%
        password:  %mailer_password%

    jms_security_extra:
        secure_controllers:  true
        secure_all_services: false

Cada entrada como ``framework`` define la configuración para un bundle.

Cada :term:`entorno` puede sobre-escribir la configuración por omisión si
provee un archivo de configuración específico:

.. code-block:: yaml

    # app/config/config_dev.yml
    imports:
        - { resource: config.yml }

    framework:
        router:   { resource: "%kernel.root_dir%/config/routing_dev.yml" }
        profiler: { only_exceptions: false }

    web_profiler:
        toolbar: true
        intercept_redirects: false

    zend:
        logger:
            priority: debug
            path:     %kernel.logs_dir%/%kernel.environment%.log

    assetic:
        use_controller: true

Extender un Bundle
~~~~~~~~~~~~~~~~~~

Además de ser una buena manera de organizar y configurar su codigo, un bundle
puede extender otro (los bundles soportan herencia). Esto le permite
sobreescribir un bundle existente para adaptar sus controladores, plantillas y
cualquier archivo que contenga. Es aquí donde los nombres lógicos resultan
útiles ya que abstraen en donde se encuentra realmente ubicado el recurso.

Para los controladores, Symfony2 automáticamente escogerá el archivo correcto de
acuerdo al árbol de herencia del bundle.

Cuando quiera hacer referencia a un archivo desde un bundle use esta notación:
``@BUNDLE_NAME/PATH_TO_FILE``; Symfony2 expandirá ``@BUNDLE_NAME`` a la ruta del
bundle. Por ejemplo, convierte ``@AcmeDemoBundle/Controller/DemoController.php``
a ``src/Acme/DemoBundle/Controller/DemoController.php``.

Para los controladores, usted necesita referenciar los nombres de los métodos:
``BUNDLE_NAME:CONTROLLER_NAME:ACTION_NAME``. Por ejemplo,
``AcmeDemoBundle:Welcome:index`` significa el método ``indexAction`` de la clase
``Acme\DemoBundle\Controller\WelcomeController``.

Para las plantillas, es aún más interesante ya que las plantillas no necesitan
ser guardadas en el sistema de ficheros. Usted puede fácilmente guardarlas, por
ejemplo, en una tabla de la base de datos. Por ejemplo,
``AcmeDemoBundle:Welcome:index.html.twig`` se convierte en
``src/Acme/DemoBundle/Resources/views/Welcome/index.html.twig``.

¿Entiende ahora por qué Symfony2 es tan flexible? Comparta sus bundles entre
aplicaciones, ubíquelas localmente o globalmente, usted escoge.

El uso de Vendors
-----------------

Lo más probable es que su aplicación vaya a depender de bibliotecas de terceros.
Estas deberían ser ubicadas en el directorio ``src/vendor/``. Este directorio
contiene inicialmente las bibliotecas de Symfony2, SwiftMailer, el ORM Doctrine,
el sistema de plantillas Twig y una selección de clases de Zend Framework.

Comprensión de la Cache y los Logs
----------------------------------

Symfony2 es probablemente uno de los frameworks *full-stack* más rapidos que
hay. ¿Pero cómo puede ser tan rápido si tiene que analizar e interpretar decenas
de archivos YAML y XML en cada *request*? Esto es en parte gracias a su sistema
de cache. La configuración de la aplicación solo es analizada durante el primer
request y luego compilada a código PHP guardado en el directorio ``app/cache/``
de la aplicación. En el entorno de desarrollo, Symfony2 es lo suficientemente
inteligente para desechar la cache cuando usted cambia un archivo. Pero en el
entorno de producción, es de usted la responsabilidad de limpiar la cache cuando
actualiza su código o cambia su configuración.

Durante el desarrollo de una aplicación web, las cosas pueden salir mal de
muchas maneras. Los archivos de log en el directorio ``app/logs/``de la
aplicación le dirán todo sobre los requests y le ayudarán a arreglar el problema
rápidamente.

Uso de la interfaz de línea de comandos
---------------------------------------

Cada aplicación viene con una herramienta de interfaz de línea de comandos
(``app/console``) que le ayuda a mantener su aplicación. Esta herramienta provee
comandos que incrementarán su productividad al automatizar tareas tediosas y
repetitivas.

Ejecútela sin ningún argumento para aprender más sobre sus capacidades:

.. code-block:: bash

    $ php app/console

La opción ``--help`` le ayudará a descubrir el uso de cada comando:

.. code-block:: bash

    $ php app/console router:debug --help

Conclusiones
------------

Llamemé loco, pero después de leer esta parte, usted debería poder
confortablemente mover las cosas y hacer que Symfony2 funcione para usted. Todo
en Symfony2 está hecho para no meterse en su camino. Así que siéntase libre de
renombrar y mover directorios como usted vea por conveniente.

Y eso es todo en este vistazo rápido. Ya sea haciendo pruebas o enviando emails,
usted aún necesita aprender mucho para volverse un maestro de Symfony2. ¿Está
listo para adentrarse en estos temas? No busque más - vaya al `libro`_ oficial y
escoja el tema que usted quiera.

.. _estándares:             http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _convención:             http://pear.php.net/
.. _libro:                  http://www.symfony-reloaded.org/learn
.. _Como autocargar clases: http://symfony.com/doc/2.0/cookbook/tools/autoloader.html
