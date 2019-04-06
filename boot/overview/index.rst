Gridgo Boot Overview
====================

Gridgo Boot is a framework to facilitate getting up and running with Gridgo. It provides an annotation-based approach, similar to Spring Boot to eliminate boilerplate code and configurations.

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

.. code-block:: xml

    @EnableComponentScan
    @Registries(defaultProfile = "local")
    public class Main {

        public static void main(String[] args) {
            GridgoApplication.run(Main.class, args);
        }
    }

``@EnableComponentScan`` will instruct Gridgo Boot to scan for any Gridgo components and start instantiating it. These include `Gateway/Processor`, `DataAccessObject`, etc.
