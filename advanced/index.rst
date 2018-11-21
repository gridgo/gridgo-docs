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

Returning responses to connectors
---------------------------------

Many Connectors require responses or acknowledgements from Processors, e.g in a 
HTTP server, you need to send the response back to client, or in Kafka you need 
to send acknowledgement back to KafkaConnector, so it will commit the message. 
This is done using the `Deferred` object in `RoutingContext`

.. code-block:: java
    
    private void handleMessages(RoutingContext rc, GridgoContext gc) {
        try {
            // do some work to get the response
            // Gridgo favors asynchronous over synchronous, so your method shouldn't block
            rc.getDeferred().resolve(response);
        } catch (Exception exception) {
            // or reject the request with some exception
            rc.getDeferred().reject(exception);
        }
    }

.. note:: Only the first call to either `resolve()` or `reject()` will work.
          Subsequent calls will be ignored.

Sending messages to gateways
----------------------------

Flows between Processors and Gateways are not one-way, most of the time. Often you will need to send messages to a remote endpoint via Gateway, e.g querying a database, or producing messages to Kafka brokers. To do so you must first obtain the Gateway instance, e.g using `GridgoContext`

.. code-block:: java

    context.findGateway("myGateway") // will return an Optional<Gateway>
           .ifPresent(gateway -> {
               // send message here
           });

The `findGateway()` method will accept a String representing the Gateway name. This is the same name you have used to open the gateway earlier.

There are 5 different types of sending:

- `void send(Message)`: Send a message to the attached conectors and forget about it. You won't know if the transportation has been successful or not
- `Promise sendWithAck(Message)`: Send a message to the attached conectors with acknowledgement. You will know the status of the transportation, but don't know about the response.
- `Promise call(Message)`: Send a message to the attached conectors and get the response. This is called RPC mode. Some connectors might not support it.
- `void push(Message)`: Simply put the message into the incoming sink of the Gateway and make it available for Processors. This operation won't involve any I/O.
- `void callAndPush(Message)`: This is similar to `Promise call(Message)`, but the response is put into the incoming sink of the Gateway instead of returning to Processor. This will make your application logic cleaner and independent of I/O, at the cost of logic fragmentation. This is inspired by the LMAX architecture.
