Gateway Basics
==============

Gateway is a level of abstraction above Connector. It is the bridge between Connector and Handler, making it easier to create event-driven application. Several connectors can be attached to one gateway, after which any requests routed to the gateway will be multicasted to the attached connectors' producers.

The complete architecture of gateways is:

.. image:: https://github.com/gridgo/gridgo/raw/master/design/gateway.png

Gateway comprises of Incoming Sink and Outgoing Sink:

Incoming sink
-------------

This sink accepts events from various types of dispatcher, including *consumers*, *timers*, *producer* and *programmatically from handlers*.

1. Consumers: Consumers read messages from a datasources (file, message queues, etc.) or accept messages from external system (e.g using a web server), then convert it to a `Message` and put into the Gateway's incoming sink.

2. Timers: Timers schedule messages to be generated and be put into the incoming sink. Each timer will accept a `Supplier<Message>`.

3. Producers: Response from the producers can be put it into the incoming sink, making them available to be consumed by other handlers.

4. Handlers: handlers can manually create the message and programmatically put it into the incoming sink.

After a message is put into the incoming sink, it will be routed to different handlers based on the configured policies and rules. The corresponding handlers will then be executed with the following parameters:

- `executionContext`: contains the `Message` to be consumed, the `DeferredObject` to be fulfilled and the caller (the gateway that this message comes from). Only the first call to resolve() or reject() will be considered, after that all subsequent calls are ignored.
- `applicationContext`: contains all application-related information, including access to gateways, configurations, etc.

Outgoing sink
-------------

Execution strategies
--------------------

Using gateways
--------------

To create a gateway, attach connectors and bind handlers

.. code-block:: java

    var appContext = new GridgoContext();
    var connector = appContext.openGateway("Orders")
                              .attachConnector("kafka:orders?brokers=127.0.0.1")
                              .attachConnector(someConnector)
                              .subscribe(handler1).withPolicy(somePolicy).finishSubscribing()
                              .subscribe(handler2).when(someCondition).with(someStrategy).finishSubscribing();

Attempting to open gateway with the same name will cause a `DuplicateGatewayException`

To access an opened gateway:

.. code-block:: java

    var gateway = appContext.findGateway("Orders"); // return a Optional<Gateway>
    if (gateway.isPresent()) {
        // do some works with the gateway
    }

or access the list of opened gateway

.. code-block:: java
    
    var gateways = appContext.getGateways(); // return a List<Gateway>
