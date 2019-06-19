Gridgo Boot Dependency Injection
================================

Gridgo Boot supports a simple built-in DI mechanism. First you create a class annotated with one of the following: `@Component`, `@Gateway`, `@DataAccessObject`. Then that class will be available to be injected into other classes using `@ComponentInject`, `@GatewayInject` and `@DataAccessInject`, respectively.

Examples:

1. Inject a `@Component` class:

.. code-block:: java

    @Component
    class Foo {
    
    }
    
Now inject Foo to other classes. Only classes annotated with @Component or @Gateway can be injected into

.. code-block:: java

    @Component
    class Bar {
    
        @ComponentInject
        private Foo foo;
    }
    
    @Gateway("a_gateway")
    @Connect("a_connector")
    class SomeProcessor implements Processor {
    
        @ComponentInject
        private Foo foo;        
    }

2. Inject a `@Gateway` producer class

.. code-block:: java

    @Gateway("foo_gateway")
    @Connect("a_connector_with_producer")
    class SomeProducer {
    }

Now inject the producer to another class. Only classes annotated with @Component or @Gateway can be injected into

.. code-block:: java

    @Gateway("bar_gateway")
    @Connect("bar_connector)
    class SomeProducer implements Processor {
    
        @GatewayInject("foo_gateway")
        private Gateway foo;
    }

You may notice that in case of `@GatewayInject`, we need to specify the gateway name, and use `io.gridgo.core.Gateway` for the injected property instead of the actual gateway class. This is because it doesn't make any sense, since the class is usually an empty class anyway.

3. Inject a @DataAccessObject class

