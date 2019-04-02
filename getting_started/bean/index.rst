Bean Basics
===========

overview
--------

Beans are the abstract data structure of Gridgo aim to transparent data format and make easier to sending via network.
Bean's structure is json-like, which support reference in additional.

hierachy
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

definition
----------

BElement is the top of all bean type.
BValue wrap any value which is primitive: Boolean, Character, String, Number (byte, short, int, long, float, double, BigInteger, BigDecimal), raw binary (byte[]). Specially, BValue can convert any primitive type to any other.
BContainer define any kind of BElement which can contain any other BElement.
BObject define a key-value data structure
BArray define a sequence data structure

usage
-----

Using static methods define in top interface to create correlative instance:

mutable instances
~~~~~~~~~~~~~~~~~

- BReference.of(data): create a mutable instance of BReference
- BValue.of(data): create a mutable instance of BValue which wrap data as its source.
- BObject.ofEmpty(), BObject.of(Map<?,?>): create a mutable instance of BObject which is empty or auto convert map to a BObject
- BArray.ofEmpty(), BArray.of(collection or array): create a mutable instance of BArray which is empty or auto convert (collection | array) into a BArray

in case you don't know which type of data want to convert to BElement, use: BElement.ofAny(data)

immutable instances
~~~~~~~~~~~~~~~~~~~

- BObject.wrap(map): wrap the input map in a WrappedImmutableBObject
- BArray.wrap(collecion or array): wrap the input (collection or array) in a WrappedImmutableBArray

In case you don't know which kind of data which want to wrap, use: BElement.wrapAny(data)

serialization
-------------

BElement support multi kind of data serialization:

build-in
~~~~~~~~

- Json: can be used by `BElement.toJson()` and `BElement.fromJson(...)`
- XML: can be used by `BElement.toXml()` and `BElement.fromXml(...)`
- Msgpack: canbe use by `BElement.toBytes()` and `BElement.fromBytes(...)`

pluggable
~~~~~~~~~

By design, every serialization format defined by a BSerializer, include above build-in kinds.
For example, by a global way you can use: `BElement.fromBytes(bytes, "json")` where "json" indicate the BSerializer instance named "json" will be used to deserialize bytes
To write you own serializer, create a class which implements BSerializer and annotate it by @BSerializationPlugin(name). After create, register it with BSerializerRegistry by call:
`BFactory.DEFAULT.getSerializerRegistry().scan(<package_name>)`
