Bean Basics
===========

Overview
--------

Beans are the abstract data structure of Gridgo aim to transparent data format and make easier to sending via network.

Bean's structure is json-like, which support reference in additional.

To use bean in your project, just add maven dependency:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean</artifactId>
        <version>${gridgo.bean.version}</version>
    </dependency>


Hierarchy
--------

.. code::

    BElement ------------------------------------------------------ AbstractBElement
        |                                                                  |
        |                                                    +-------------+------------+
        |                                                    |             |            |
        +-- BValue ----------------------------------- MutableBValue       |            |
        |                                                                  |            |
        +-- BReference ------------------------------------------- MutableBReference    |
        |                                                                               |
        +-- BContainer -------------------------------------------------------- AbstractBContainer
                |                                                                       |
                |                                                           +-----------+-----------+
                |                                                           |                       |
                +-- BObject ---------------------------------------- AbstractBObject                |
                |       |                                                   |                       |
                |       +-- WrappedBObject ----+                +-----------+----------+            |
                |       |                      |                |                      |            |
                |       +-- ImmutableBObject --+------ WrappedImmutableBObject    MutableBObject    |
                |                                                                                   |
                +-- BArray ------------------------------------------------------------------ AbstractBArray
                        |                                                                           |
                        +-- WrappedBArray -----+                                        +-----------+-----------+
                        |                      |                                        |                       |
                        +-- ImmutableBArray ---+---------------------------- WrappedImmutableBArray        MutableBArray

Definition
----------

BElement
    is the ancestor of all bean types. It's useful if you want to create a bean from any type.

BReference
    wraps any object as a reference.

BValue
    wraps any value which is primitive: `Boolean`, `Character`, `String`, `Number` (byte, short, int, long, float, double, BigInteger, BigDecimal), `raw binary` (byte[]). BValue can also convert any primitive to other (primitive) types.

BContainer
    base class for any BElement type which can contains other BElement.

BObject
    defines a key-value data structure.

BArray
    defines a sequence data structure.

Usage
-----

Using static methods define in top interface to create correlative instance:

Mutable instances
~~~~~~~~~~~~~~~~~

- ``BReference.of(data)``: create a mutable instance of BReference
- ``BValue.of(data)``: create a mutable instance of BValue which wrap data as its source.
- ``BObject.ofEmpty()``, ``BObject.of(Map<?,?>)``: create a mutable instance of BObject which is empty or auto convert map to a BObject
- ``BArray.ofEmpty()``, ``BArray.of(collection or array)``: create a mutable instance of BArray which is empty or auto convert (collection | array) into a BArray

in case you don't know which type of data want to convert to BElement, use: ``BElement.ofAny(data)``

Immutable instances
~~~~~~~~~~~~~~~~~~~

- ``BObject.wrap(map)``: wrap the input map in a ``WrappedImmutableBObject``
- ``BArray.wrap(collecion or array)``: wrap the input (collection or array) in a ``WrappedImmutableBArray``

In case you don't know which kind of data which want to wrap, use: ``BElement.wrapAny(data)``

Working with pojo
~~~~~~~~~~~~~~~~~

If you have a pojo object and want to convert it to BObject, you can use ``BObject.ofPojo()``.

Serialization
-------------

BElement support multi kind of data serialization:

Built-in
~~~~~~~~

- JSON
    can be accessed by ``BElement.toJson()`` and ``BElement.fromJson(...)``
- XML
    can be accessed by ``BElement.toXml()`` and ``BElement.fromXml(...)``
- Msgpack
    can be accessed by ``BElement.toBytes()`` and ``BElement.fromBytes(...)`` (`msgpack` set as default binary serializer if the system property name `gridgo.bean.serializer.binary.default` is unset)
