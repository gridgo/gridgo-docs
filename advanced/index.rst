Advanced topics
===============

This article will discuss multiple advanced topics of each Gridgo components: 
``Gateway``, ``Connector`` and ``GridgoContext``

Using a custom Registry
-----------------------

Normally connectors will only require simple key-value configuration to work,  but 
some might require specific **bean instances**. Bean are created and registered in 
``Registry``, where they can be *lookup* later. One example is the `MongoDBConnector`
which requires an instance of `MongoClient`. To use those connectors, you will need
to create the bean instances and register them inside the Registry.

.. code-block:: java

    var mongoClient = MongoClient.create(); // create the MongoClient bean
    var myRegistry = new SimpleRegistry().register("mongoBean", mongoClient); // register the bean
    var context = new DefaultGridgoContextBuilder().setName("application").setRegistry(myRegistry).build();

.. note:: It's currently not supported to register bean instances after you have built the 
          GridgoContext, so make sure you have registered all the beans you need.

Configuring an execution policy
------------------------------

When subscribing to Gateway's incoming messages, you can optionally configure an 
**Execution Policy**. Execution policies allow you to control in which condition
and how the processors will be executed. For example, to only execute a processor
in a particular condition:

.. code-block:: java

    var gateway = context.openGateway("myGateway")
                         .attachConnector("kafka:mytopic")
                         .subscribe(this::handleMessages)
                         .when("payload.body.data > 1") // only execute the Processor if payload body is numeric and greater than 1
                         .finishSubscribing().get();
                         
Or, to execute the processor using a particular strategy, `ExecutorService` for instance:

.. code-block:: java

    var gateway = context.openGateway("myGateway")
                         .attachConnector("kafka:mytopic")
                         .subscribe(this::handleMessages)
                         .when("payload.body.data > 1")
                         .using(new ExecutorExecutionStrategy(8))
                         .finishSubscribing().get();

Calling `.subscribe()` will actually return a ``HandlerSubscription``, which you
can call `.when()` and `.using()` upon. This is called fluent-style API. To return
the flow back to Gateway, you will call `.finishSubscribing()`.
