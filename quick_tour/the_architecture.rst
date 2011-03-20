La arquitectura
===============

¡Usted es mi héroe! ¿Quién creería que seguiría aquí luego de las tres primeras
partes? Sus esfuerzos serán prontamente recompensados. Las primeras tres partes
revisaron muy vagamente la arquitectura del framework. Ya que esta es lo que
distingue a Symfony2 de otros frameworks, concentrémonos en ella.

.. index::
   single: Estructura de directorios

La estructura de directorios
----------------------------

La estructura de directorios en una :term:`aplicación` de Symfony2 es bastante
flexible, pero la estructura de directorios del *sandbox* refleja la estructura
típica y recomendada de una aplicación de Symfony2:

* ``app/``: La configuración de la aplicación;
* ``src/``: El código PHP del proyecto;
* ``vendor/``: Las dependencias a terceros;
* ``web/``: El directorio web raíz.

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

El kernel requiere primeramente al archivo ``bootstrap.php``, el cual inicia el
framework y registra al *autoloader* (ver abajo).

Como cualquier controlador frontal, ``app.php`` usa una clase Kernel,
``AppKernel``, para iniciar la aplicación.

.. index::
   single: Kernel

El directorio de la aplicación
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

La clase ``AppKernel`` es el punto de ingreso principal a la configuración de la
aplicación y como tal, se ubica en el directorio ``app/``.

Esta clase debe implementar tres métodos:

* ``registerRootDir()``: Devuelve el directorio de configuración;

* ``registerBundles()``: Devuelve un *array* de todos los bundles necesarios
  para correr la aplicación (atento a la referencia a
  ``Sensio\HelloBundle\HelloBundle``);

* ``registerContainerConfiguration()``: Carga la configuración (luego veremos
  más sobre esto);

Échele un vistazo a la implementación por omisión de estos métodos para que
entienda mejor la flexibilidad del framework.

La auto-carga de PHP puede ser configurada vía ``autoload.php``::

    // app/autoload.php
    use Symfony\Component\ClassLoader\UniversalClassLoader;

    $loader = new UniversalClassLoader();
    $loader->registerNamespaces(array(
        'Symfony'                        => __DIR__.'/../vendor/symfony/src',
        'Sensio'                         => __DIR__.'/../src',
        'Doctrine\\Common\\DataFixtures' => __DIR__.'/../vendor/doctrine-data-fixtures/lib',
        'Doctrine\\Common'               => __DIR__.'/../vendor/doctrine-common/lib',
        'Doctrine\\DBAL\\Migrations'     => __DIR__.'/../vendor/doctrine-migrations/lib',
        'Doctrine\\MongoDB'              => __DIR__.'/../vendor/doctrine-mongodb/lib',
        'Doctrine\\ODM\\MongoDB'         => __DIR__.'/../vendor/doctrine-mongodb-odm/lib',
        'Doctrine\\DBAL'                 => __DIR__.'/../vendor/doctrine-dbal/lib',
        'Doctrine'                       => __DIR__.'/../vendor/doctrine/lib',
        'Zend'                           => __DIR__.'/../vendor/zend/library',
    ));
    $loader->registerPrefixes(array(
        'Twig_Extensions_' => __DIR__.'/../vendor/twig-extensions/lib',
        'Twig_'            => __DIR__.'/../vendor/twig/lib',
        'Swift_'           => __DIR__.'/../vendor/swiftmailer/lib/classes',
    ));
    $loader->register();

El cargador de clases universal de Symfony2 (``UniversalClassLoader``) se
utiliza para auto-cargar archivos que respetan ya sea a los `estándares`_ de
inter-operabilidad técnica para *namespaces* de PHP 5.3 o la `convención`_ de
PEAR para nombrar clases. Como puede ver aquí, todas las dependencias están
ubicadas dentro del directorio ``vendor/``, pero esto es solo una convención.
Usted puede ubicarlos donde sea que quiera, en una ubicación global en su
servidor, o en una local en cada proyecto.

.. index::
   single: Bundles

El sistema de Bundles
---------------------

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

Una aplicación está hecha de bundles tal como está definido en el método
``registerBundles()`` de la clase ``AppKernel``::


    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),

            // habilita bundles de terceros
            new Symfony\Bundle\ZendBundle\ZendBundle(),
            new Symfony\Bundle\SwiftmailerBundle\SwiftmailerBundle(),
            new Symfony\Bundle\DoctrineBundle\DoctrineBundle(),
            //new Symfony\Bundle\DoctrineMigrationsBundle\DoctrineMigrationsBundle(),
            //new Symfony\Bundle\DoctrineMongoDBBundle\DoctrineMongoDBBundle(),

            // registra sus bundles
            new Sensio\HelloBundle\HelloBundle(),
        );

        if ($this->isDebug()) {
            $bundles[] = new Symfony\Bundle\WebProfilerBundle\WebProfilerBundle();
        }

        return $bundles;
    }

Además de ``HelloBundle`` del que ya hemos hablado, vea que el kernel también
habilita ``FrameworkBundle``, ``DoctrineBundle``, ``SwiftmailerBundle`` y
``ZendBundle``. Todos ellos son parte del framework base.

Cada bundle puede ser adaptado vía archivos de configuración escritos en YAML,
XML o PHP. Dele un vistazo a la configuración por omisión:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        framework:
            charset:       UTF-8
            error_handler: null
            csrf_protection:
                enabled: true
                secret: xxxxxxxxxx
            router:        { resource: "%kernel.root_dir%/config/routing.yml" }
            validation:    { enabled: true, annotations: true }
            templating:    { engines: ['twig'] } #assets_version: SomeVersionScheme
            session:
                default_locale: en
                lifetime:       3600
                auto_start:     true

        # Twig Configuration
        twig:
            debug:            %kernel.debug%
            strict_variables: %kernel.debug%

        ## Doctrine Configuration
        #doctrine:
        #   dbal:
        #       dbname:   xxxxxxxx
        #       user:     xxxxxxxx
        #       password: ~
        #       logging:  %kernel.debug%
        #   orm:
        #       auto_generate_proxy_classes: %kernel.debug%
        #       mappings:
        #           HelloBundle: ~

        ## Swiftmailer Configuration
        #swiftmailer:
        #    transport:  smtp
        #    encryption: ssl
        #    auth_mode:  login
        #    host:       smtp.gmail.com
        #    username:   xxxxxxxx
        #    password:   xxxxxxxx

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <framework:config charset="UTF-8" error-handler="null" cache-warmer="false">
            <framework:router resource="%kernel.root_dir%/config/routing.xml" cache-warmer="true" />
            <framework:validation enabled="true" annotations="true" />
            <framework:session default-locale="en" lifetime="3600" auto-start="true" />
            <framework:templating assets-version="SomeVersionScheme" cache-warmer="true">
                <framework:engine id="twig" />
            </framework:templating>
            <framework:csrf-protection enabled="true" secret="xxxxxxxxxx" />
        </framework:config>

        <!-- Twig Configuration -->
        <twig:config debug="%kernel.debug%" strict-variables="%kernel.debug%" cache-warmer="true" />

        <!-- Doctrine Configuration -->
        <!--
        <doctrine:config>
            <doctrine:dbal dbname="xxxxxxxx" user="xxxxxxxx" password="" logging="%kernel.debug%" />
            <doctrine:orm auto-generate-proxy-classes="%kernel.debug%">
                <doctrine:mappings>
                    <doctrine:mapping name="HelloBundle" />
                </doctrine:mappings>
            </doctrine:orm>
        </doctrine:config>
        -->

        <!-- Swiftmailer Configuration -->
        <!--
        <swiftmailer:config
            transport="smtp"
            encryption="ssl"
            auth-mode="login"
            host="smtp.gmail.com"
            username="xxxxxxxx"
            password="xxxxxxxx" />
        -->

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('framework', array(
            'charset'         => 'UTF-8',
            'error_handler'   => null,
            'csrf-protection' => array('enabled' => true, 'secret' => 'xxxxxxxxxx'),
            'router'          => array('resource' => '%kernel.root_dir%/config/routing.php'),
            'validation'      => array('enabled' => true, 'annotations' => true),
            'templating'      => array(
                'engines' => array('twig'),
                #'assets_version' => "SomeVersionScheme",
            ),
            'session' => array(
                'default_locale' => "en",
                'lifetime'       => "3600",
                'auto_start'     => true,
            ),
        ));

        // Configuración de Twig
        $container->loadFromExtension('twig', array(
            'debug'            => '%kernel.debug%',
            'strict_variables' => '%kernel.debug%',
        ));

        // Configuración de Doctrine
        /*
        $container->loadFromExtension('doctrine', array(
            'dbal' => array(
                'dbname'   => 'xxxxxxxx',
                'user'     => 'xxxxxxxx',
                'password' => '',
                'logging'  => '%kernel.debug%',
            ),
            'orm' => array(
                'auto_generate_proxy_classes' => '%kernel.debug%',
                'mappings' => array('HelloBundle' => array()),
            ),
        ));
        */

        // Configuración de Swiftmailer
        /*
        $container->loadFromExtension('swiftmailer', array(
            'transport'  => "smtp",
            'encryption' => "ssl",
            'auth_mode'  => "login",
            'host'       => "smtp.gmail.com",
            'username'   => "xxxxxxxx",
            'password'   => "xxxxxxxx",
        ));
        */

Cada entrada como ``framework`` define la configuración para un bundle.

Cada :term:`entorno` puede sobre-escribir la configuración por omisión si
provee un archivo de configuración específico:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_dev.yml
        imports:
            - { resource: config.yml }

        framework:
            router:   { resource: "%kernel.root_dir%/config/routing_dev.yml" }
            profiler: { only_exceptions: false }

        web_profiler:
            toolbar: true
            intercept_redirects: true

        zend:
            logger:
                priority: debug
                path:     %kernel.logs_dir%/%kernel.environment%.log

    .. code-block:: xml

        <!-- app/config/config_dev.xml -->
        <imports>
            <import resource="config.xml" />
        </imports>

        <framework:config>
            <framework:router resource="%kernel.root_dir%/config/routing_dev.xml" />
            <framework:profiler only-exceptions="false" />
        </framework:config>

        <webprofiler:config
            toolbar="true"
            intercept-redirects="true"
        />

        <zend:config>
            <zend:logger priority="info" path="%kernel.logs_dir%/%kernel.environment%.log" />
        </zend:config>

    .. code-block:: php

        // app/config/config_dev.php
        $loader->import('config.php');

        $container->loadFromExtension('framework', array(
            'router'   => array('resource' => '%kernel.root_dir%/config/routing_dev.php'),
            'profiler' => array('only-exceptions' => false),
        ));

        $container->loadFromExtension('web_profiler', array(
            'toolbar' => true,
            'intercept-redirects' => true,
        ));

        $container->loadFromExtension('zend', array(
            'logger' => array(
                'priority' => 'info',
                'path'     => '%kernel.logs_dir%/%kernel.environment%.log',
            ),
        ));

¿Entiende ahora por qué Symfony2 es tan flexible? Comparta sus bundles entre
aplicaciones, ubíquelas localmente o globalmente, usted escoge.

.. index::
   single: Vendors

El uso de Vendors
-----------------

Lo más probable es que su aplicación vaya a depender de bibliotecas de terceros.
Estas deberían ser ubicadas en el directorio ``src/vendor/``. Este directorio
contiene inicialmente las bibliotecas de Symfony2, SwiftMailer, el ORM Doctrine,
el sistema de plantillas Twig y una selección de clases de Zend Framework.

.. index::
   single: Cache de configuración
   single: Logs

La Cache y los Logs
-------------------

Symfony2 es probablemente uno de los frameworks *full-stack* más rapidos que
hay. ¿Pero cómo puede ser tan rápido si tiene que analizar e interpretar decenas
de archivos YAML y XML en cada *request*? Esto es en parte gracias a su sistema
de cache. La configuración de la aplicación solo es analizada durante el primer
request y luego compilada a código PHP guardado en el directorio ``cache/`` de
la aplicación. En el entorno de desarrollo, Symfony2 es lo suficientemente
inteligente para desechar la cache cuando usted cambia un archivo. Pero en el
entorno de producción, es de usted la responsabilidad de limpiar la cache cuando
actualiza su código o cambia su configuración.

Durante el desarrollo de una aplicación web, las cosas pueden salir mal de
muchas maneras. Los archivos de log en el directorio ``logs/``de la aplicación
le dirán todo sobre los requests y le ayudarán a arreglar el problema rápidamente.

.. index::
   single: CLI
   single: Línea de comandos

La interfaz de línea de comandos
--------------------------------

Cada aplicación viene con una herramienta de interfaz de línea de comandos
(``console``) que le ayuda a mantener su aplicación. Esta herramienta provee
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

.. _estándares: http://groups.google.com/group/php-standards/web/psr-0-final-proposal
.. _convención: http://pear.php.net/
.. _libro:      http://www.symfony-reloaded.org/learn
