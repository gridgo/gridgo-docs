Bridge and Switch
=================

Sometimes, you want a message coming from a Gateway's incoming sink to be automatically routed to another Gateway, then Bridge and Switch will come in handy. Some examples might be:

- Retrieve messages from Kafka, do some transformation and store it in Mongo
- Retrieve messages from incoming HTTP requests and routed it to a business logic Gateway

The different between Bridge and Switch is that Bridge will route the messages to the target Gateway's outgoing sink (using `send()`, while a Switch will route the messages to the target Gateway's incoming sink (using `push()`).

For example, the following code will use Bridge to store messages coming from Kafka to MongoDB:

.. code-block:: java

    context.openGateway("kafka")
           .attachConnector("kafka:someTopic?...");
    context.openGateway("mongo")
           .attachConnector("mongo:mongoClient/test_db/test_collection?...");
    context.attachComponent(new BridgeComponent("kafka", "mongo", this::transformMessage));
    
Note that the `transformMessage()` method will convert the message from Kafka format to MongoDB format.
