Install Gridgo
==============

Gridgo can be easily installed using Maven. It is also split into
several independent components that can be installed separately:

**Install gridgo-core**

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-core</artifactId>
        <version>0.1.2</version>
    </dependency>

**Install gridgo-connector-core**

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-connector-core</artifactId>
        <version>0.1.0</version>
    </dependency>

To work with a specific connector type (e.g ``gridgo-kafka``) you
also need to install it separately:

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-kafka</artifactId>
        <version>same as gridgo-connector-core</version>
    </dependency>
