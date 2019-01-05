Using a custom Registry
=======================

Normally connectors will only require simple key-value configuration to work,  but 
some might require specific **bean instances**. Bean are created and registered in 
``Registry``, where they can be *lookup* later. One example is the `MongoDBConnector`
which requires an instance of `MongoClient`. To use those connectors, you will need
to create the bean instances and register them inside the Registry.

.. code-block:: java

    // create the MongoClient bean
    var mongoClient = MongoClient.create(); 

    // register the bean
    var myRegistry = new SimpleRegistry().register("mongoBean", mongoClient); 
    
    // create the context with the custom registry
    var context = new DefaultGridgoContextBuilder().setName("application").setRegistry(myRegistry).build();
    
    // later on you can look up the bean using the provided name
    mongoClient = context.getRegistry().lookup("mongoBean", MongoClient.class);
    
Another use case of Registry is to store some settings, which can be required for
application to run. Since 0.2.0 you can also substitute the settings value in a 
connector endpoint.

.. note:: Since 0.2.0, you can register new entry after creating the Registry.
