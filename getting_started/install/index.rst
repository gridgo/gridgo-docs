Install Gridgo
==============

Gridgo requires Java 11 so you should make sure `JDK 11 <https://www.oracle.com/technetwork/java/javase/downloads/index.html>`_ is installed. Gridgo is tested with ``Oracle JDK`` but any other JDK should be fine.

Gridgo itself can be easily installed using Maven. It is also split into
several independent components that can be installed separately:

**Install gridgo-core**

This is the recommended package, which provides the most functionalities of Gridgo (``Gateway``, ``Application Context``)

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-core</artifactId>
        <version>0.1.2</version>
    </dependency>

**Install gridgo-connector-core**

Install this if you want to only work with the I/O abstraction layer (``Connector``)

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-connector-core</artifactId>
        <version>0.1.0</version>
    </dependency>

.. note:: To work with a specific connector type (e.g `gridgo-kafka`) you
          also need to install it separately:

.. code-block:: xml
    
    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-kafka</artifactId>
        <version>same as gridgo-connector-core</version>
    </dependency>

A full list of supported connectors can be found on the
`GitHub repository <https://github.com/gridgo/gridgo-connector/tree/master/connectors>`_
