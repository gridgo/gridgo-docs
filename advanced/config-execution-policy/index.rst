Configuring an execution policy
===============================

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
