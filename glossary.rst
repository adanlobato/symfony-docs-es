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

   Front Controller
        A *Front Controller* is a short PHP that lives in the web directory
        of your project. Typically, *all* requests are handled by executing
        the same front controller, whose job is to bootstrap the Symfony
        application.

   Service
        A *Service* is a generic term for any PHP object that performs a
        specific task. A service is usually used "globally", such as a database
        connection object or an object that delivers email messages. In Symfony2,
        services are often configured and retrieved from the service container.
        An application that has many decoupled services is said to follow
        a `service-oriented architecture`_.

   Service Container
        A *Service Container*, also known as a *Dependency Injection Container*,
        is a special object that manages the instantiation of services inside
        an application. Instead of creating services directly, the developer
        *trains* the service container (via configuration) on how to create
        the services. The service container takes care of lazily instantiating
        and injecting dependent services.

   HTTP Specification
        The *Http Specification* is a document that describes the Hypertext
        Transfor Protocol - a set of rules laying out the classic client-server
        request-response communication. The specification defines the format
        used for a request and response as well as the possible HTTP headers
        that each may have. For more information, read the `Http Wikipedia`_
        article or the `HTTP 1.1 RFC`_.

.. _`service-oriented architecture`: http://wikipedia.org/wiki/Service-oriented_architecture
.. _`HTTP Wikipedia`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
.. _`HTTP 1.1 RFC`: http://www.w3.org/Protocols/rfc2616/rfc2616.html
