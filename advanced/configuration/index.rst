Using configuration with Gridgo
==============================

Gridgo supports two types of configuration: a *key-value* config and an *object-based* config. Key-value configurations are usually good for storing application settings, similar to a properties file, or bean registration, similar to Spring bean. Object-based configurations on the other hand suitable for storing complex data structure, like when configuring `GridgoContext`. Refer to each section below to get more details about each type of configuration.

Key-value configuration
-----------------------

Key-value configurations in Gridgo are called `Registry`. A registry stores a mapping between a String and an arbitrary object. Two most basic operations are `lookup()` and `register()`, e.g:

.. code-block:: java

    var registry = new SimpleRegistry();
    registry.register('numbers', new int[] {1, 2, 3});
    var numbers = registry.lookup('numbers');
    
You can also pass a type to the `lookup` method, so result will be automatically casted the specified type:

.. code-block:: java

    var numbers = registry.lookup('numbers', int[].class);
    
And substitute placeholders in strings using values from Registry:

.. code-block:: java

    var someString = "mongodb://${mongodb.host}:${mongodb.port}";
    var parsedString = registry.substitute(someString);

Some of the supported registries are:

- `SimpleRegistry`: a registry backed by a HashMap
- `PropertiesFileRegistry`: a registry backed by a properties file
- `SystemEnvRegistry`: a registry which corresponds to the system environment variables
- `SystemPropertyRegistry`: a registry which corresponds to the Java properties (e.g when using `-D` option)
- `MultiSourceRegistry`: a registry which contains other registries, so you can use multiple registries as if it was a single one.

There are some registries supported in `gridgo-extras`:

- SpringRegistry: a registry which is backed by Spring `ApplicationContext`
- TypeSafeRegistry: a registry which is backed by typesafe/config

Object-based configuration
--------------------------

Object-based configurations in Gridgo are called `Configurator`. They are a bit more advanced than `Registry` which can support hot-reload. But they don't support looking up individual key inside the configurations. Rather they will return the whole configuration as a `BElement`.

.. code-block:: java

    // create an instance of configurator using TypeSafe
    var configurator = TypeSafeConfigurator.ofResource("application.conf");
    
    // register for configuration event
    configurator.subscribe(event -> {
      if (event.isLoaded())
        System.out.println("Event loaded: " + event.asLoaded().getConfigObject());
      else if (event.isReloaded())
        System.out.println("Event reloaded: " + event.asLoaded().getConfigObject());
      else if (event.isFailed())
        System.out.println(event.asFailed().getCause().printStackTrace());
    });
    
    // start the configurator
    configurator.start();
    
The configurator needs more work than Registry because it can support hot-reload and remote configuration (e.g using a Database or Zookeeper)

.. tip:: Most configurators are extended from `ReplayEventDispatcher`, that means you can subscribe whenever you want, all              events will be replayed the first time you subscribe.
