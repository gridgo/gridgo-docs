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
