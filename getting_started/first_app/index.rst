Creating first application with Gridgo
======================================

This article will help you creating your first Gridgo application. This simple application will do the following:

- Create a new application context
- Open a gateway and attach a HTTP server to it (using `gridgo-vertx-http`)
- Start listening for incoming HTTP requests and return the same as responses.

.. tip:: The full source code for this example can be found in the :ref:`Examples <sec-examples>` section

The entry-point of a Gridgo application is the ``GridgoContext``. A GridgoContext will act as a standalone component which will have its own configuration and be started/stopped independently regardless of where it's running. While a JVM process is a physical entity, a GridgoContext is a logical one, and in fact, you can have multiple instances of GridgoContext inside a single JVM process.

GridgoContext can be created using a ``GridgoContextBuilder``, which currently supports ``DefaultGridgoContextBuilder``

.. code-block:: java

    // create the context using default configuration
    var context = new DefaultGridgoContextBuilder().setName("application").build();

GridgoContext allows you to open ``Gateway``. Gateway is the abstraction level between the I/O layer (``Connector``) and business logic code. It allows you to write code in event-driven paradigm. You can also think of Gateway as the bridge between your application business logic and the external (remote or local) endpoints.

Gateways are asynchronous in nature, which all interactions are handled using Promise.

The following code will open a new gateway, attach an I/O connector to it and subscribe for incoming messages.

.. code-block:: java

    var gateway = context.openGateway("myGateway")
                         .attachConnector("vertx:http://127.0.0.1:8080") // attach a web server connector
                         .subscribe(this::handleMessages) // subscribe for incoming messages
                         .finishSubscribing().get();

More about Connector and available endpoints can be found `here <https://github.com/gridgo/gridgo-connector>`_

`handleMessages` is actually a implementation of ``Processor``, which will take 2 arguments: a ``RoutingContext`` containing information about the current request and the GridgoContext the request is associated with.

.. code-block:: java

    private void handleMessages(RoutingContext rc, GridgoContext gc) {
        var msg = rc.getMessage();
        var deferred = rc.getDeferred();
        
        // using the same request as response
        deferred.resolve(msg);
    }

After you have configured the context, you need to call its ``start()`` method, which will in turn starting gateways. If the attached connector supports a ``Consumer``, it will listen for incoming messages and route them to the matching Processors.

.. code-block:: java

    context.start();

    // Register a shutdown hook to stop the context
    Runtime.getRuntime().addShutdownHook(new Thread(context::stop));

Well done! You have created your first Gridgo application with just a few lines of code. More hand-on tutorials can be found at the :ref:`Tutorials <sec-tutorials>` section, or you can go to the next section for more advanced topics.
