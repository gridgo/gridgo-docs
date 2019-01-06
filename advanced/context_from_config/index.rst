Creating GridgoContext from configuration
=========================================

It is very convenient to create GridgoContext using configuration, whether it is HOCON, JSON or YAML. The code is very simple:

.. code-block:: java

    // create a configurator using TypeSafe, which supports JSON and HOCON
    var configurator = TypeSafeConfigurator.ofResource("gridgo-context.conf");
    
    // create the context using the configurator
    var context = new ConfiguratorContextBuilder().setRegistry(registry)
                                                  .setConfigurator(configurator)
                                                  .build();
                                                  
Following are an example of a conf file:

.. code-block:: hocon

    # the application name
    applicationName = "promo-rules-monitoring"

    # list of gateways
    gateways {
    
        # define a gateway with name "test" and one subscriber
        test.subscribers += "class:io.gridgo.example.TestProcessor"
        
        # add another gateway with name "another" and one subscriber
        # with a custom execution strategy and condition
        another.subscribers += {
            processor = "class:io.gridgo.example.AnotherProcessor"
            executionStrategy = "bean:myExecutionStrategy"
            condition = "payload.body.data not empty"
        }
        
        # add another gateway with 2 connectors and autoStart false
        alsoAnother {
            autoStart = false
            connectors += "kafka:topic1?brokers=localhost:9092"
            
            # this connector will have a custom ConnectorContextBuilder
            connectors += {
                endpoint = "vertx:http://localhost:8080"
                contextBuilder = "bean:myConnectorContextBuilder"
            }
            subscribers += "class:io.gridgo.example.AlsoAnotherProcessor"
        }
    }
    
    # list of components
    components += "bean:myComponent"

As you can see the syntax very flexible: for `subscribers`, `connectors` you can use either String or Object. The syntax is based on HOCON format, a superset of JSON, which is optimized for human.

For `processor`, you can either use a bean or a class. If you use a bean, it must be registered in the `Registry` object passed to the `ConfiguratorContextBuilder`. If you use a class, it must have exactly one constructor, preferrably non-args. If you use args constructor, the arguments will be looked up from the Registry based on their types. If there are multiple beans applicable for one arguments, it will throw a `AmbigiousException`.
