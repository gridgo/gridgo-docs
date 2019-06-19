Gridgo Boot Overview
====================

Gridgo Boot is a framework to facilitate getting up and running with Gridgo. It provides an annotation-based approach, similar to Spring Boot to eliminate boilerplate code and tedious configurations.

Install
-------

To install Gridgo Boot with Maven:

.. code-block:: xml

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-boot</artifactId>
        <version>${gridgo.version}</version>
    </dependency>

Getting started
---------------
 
To start using Gridgo Boot, you need a Java main class.

.. code-block:: java

    @EnableComponentScan
    @Registries(defaultProfile = "local")
    public class Main {

        public static void main(String[] args) {
            GridgoApplication.run(Main.class, args);
        }
    }

``@EnableComponentScan`` will instruct Gridgo Boot to scan for any Gridgo components and start instantiating it. These includes:

`@Gateway`
    Annotating that the class represents a Gateway, which can be attached with `@Connector`. If the class is an implementation of `Processor`, it will be subscribed to the gateway automatically too. This class will be scanned further for additional field annotations (e.g @RegistryInject, @ComponentInject...). Full documentation will be covered at the Dependency Injection section.

`@DataAccessObject`
    Annotating that the class represents a DAO. The class must be an interface. Full documentation will be covered at the Gridgo Data section.

`@Component`
    Annotating that the class represents a generic component. It will then be available for dependency injection using `@ComponentInject`. The class instance's properties can also be injected the same way as `@Gateway`.
