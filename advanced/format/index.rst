Thread-safe
==========
By default, all BElement created using default static methods ``of[...]()`` are always *non* thread-safe.

To create a thread-safe BElement, use ``BObject.withHolder(<holder_map>)`` or ``BArray.withHolder(<holder_list>)``. Where the parameter is instance of a thread-safe type (e.g `ConcurrentHashMap`).

Pluggable serialization
=======================

Every serialization format implements `BSerializer`, include all of the built-in ones.

For example, instead of using ``BElement.ofJson(json)`` you can also use a more generalized approach: ``BElement.fromBytes(bytes, "json")``. The second parameter is the name of the `BSerializer` you registered in the Serialization Registry (more on this later). ``json``, ``xml``, ``default`` or ``msgpack`` is built-in, which means they are implicity registered.

To write you own serializer, create a class which implements `BSerializer` and annotate it by ``@BSerializationPlugin(name)``. After that, register it with `BSerializerRegistry` by calling:

.. code-block:: java

    BFactory.DEFAULT.getSerializerRegistry().scan(<package_name_of_your_serializer>);

or

.. code-block:: java

    BFactory.DEFAULT.getSerializerRegistry.register(<name>, <BSerializer instance>);

By default ``BFactory.DEFAULT.getSerializerRegistry()`` auto scan package ``io.gridgo.serialization``, so that if your custom serializer located in that package and loaded in the same class loader with BElement, you don't need to call register.

.. note:: your custom `BSerializer` must be thread-safe.

Default binary serializer
=========================

Bean using a system property named ``gridgo.bean.serializer.binary.default`` to take default binary serializer name, if it's unset, ``msgpack`` will be used.

Default serializer will be used in ``toBytes()``, ``writeBytes(...)`` and ``fromBytes(...)`` methods.

You can, for example, change the default binary format to avro by providing the correct system property value when starting the JVM:

.. code::

    -Dgridgo.bean.serializer.binary.default=avro

Schema and schema-less
======================

Schema
------

There are a lot of binary serialization format which based on a `schema` - generally understood as a value object.

To serialize an object with a schema serialization format, the object's type must be registered with the serializer.

Generally, all schema format supports 2 modes: single and multi. You use single format if your whole application only works with one schema (which is usually not the case). If you works with multiple schemas, there are 2 ways you can do:

- Use multi schemas mode: Using this you need to register the schemas with the serializer. The serialized data will then be prepended with a `type` header (commonly using 4 bytes integer), which might not be inter-operable.

- Use many single-schema mode: Using this you need to create individual serializer for each schema. The advantage is that the data won't be prepended with a `type` header, thus it's more inter-operable.

There are 2 interfaces for `schema serialization`: ``io.gridgo.bean.serialization.MultiSchemaSerializer<S>`` and ``io.gridgo.bean.serialization.SingleSchemaSerializer<S>``

Schema-less
-----------

`JSON`, `XML`, `msgpack` (all of the built-in formats) are schema-less, which mean they don't need a pre-defined schema class.

Pre-support serialization
=========================

Gson
----

By default, `json` serializer using ``json-smart`` lib, but if you have your own reason to use ``gson``, you can do it by add maven dependency:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-gson</artifactId>
        <version>x.x.x</version>
    </dependency>

Then, you can serialize using ``gson`` by calling: ``BElement.toBytes('gson');``

Protobuf
--------

Protobuf is a popularly serialization format. Gridgo-bean already support it with a dependency:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-protobuf</artifactId>
        <version>x.x.x</version>
    </dependency>

Protobuf serializer support 2 modes:

- Single schema:

First you need to register the schema once when you start the application:

.. code-block:: java

    ProtobufSingleSchemaSerializer protobufSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(ProtobufSingleSchemaSerializer.NAME);
    protobufSerializer.setSchema(Person.class);

Then you can start using it:

.. code-block:: java

    // create a person instance
    Person p = createPerson();
    BElement ele = BElement.ofAny(p);
    
    // serialize the person instance using Protobuf format
    byte[] bytes = ele.toBytes(ProtobufSingleSchemaSerializer.NAME);

    // deserialize it
    BElement unpackedEle = BElement.ofBytes(bytes, ProtobufSingleSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    // the two should be equals
    Assert.assertEquals(p, p2);

- Multi schema:

First you need to register the schema once when you start the application. You need to associate the schema with a `type` (an integer):

.. code-block:: java

    ProtobufMultiSchemaSerializer protobufSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(ProtobufMultiSchemaSerializer.NAME);
    protobufSerializer.registerSchema(Person.class, 1);

Then you can start using it as normal:

.. code-block:: java

    Person p = createPerson();

    BElement ele = BElement.ofAny(p);
    byte[] bytes = ele.toBytes(ProtobufMultiSchemaSerializer.NAME);

    BElement unpackedEle = BElement.ofBytes(bytes, ProtobufMultiSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

``Person`` is a protobuf generated class.

.. note:: you must register the schema class before using `protobuf` serialization format. Only `BReference` contains registered `schema` can be serialized/deserialized

Avro
----

Like protobuf, Avro is also a widely-used serialization format. To use it, add below lines to your pom.xml:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-avro</artifactId>
        <version>x.x.x</version>
    </dependency>

Avro serializier also support 2 modes:

- Single schema:
.. code-block:: java

    AvroSingleSchemaSerializer avroSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(AvroSingleSchemaSerializer.NAME);
    avroSerializer.setSchema(Person.class);

    Person p = createPerson();
    byte[] bytes = BElement.ofAny(p).toBytes(AvroSingleSchemaSerializer.NAME);

    BElement unpackedEle = BElement.ofBytes(bytes, AvroSingleSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

- Multi schema:
.. code-block:: java

    AvroMultiSchemaSerializer avroSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(AvroMultiSchemaSerializer.NAME);
    avroSerializer.registerSchema(Person.class, 1);

    Person p = createPerson();
    byte[] bytes = BElement.ofAny(p).toBytes(AvroMultiSchemaSerializer.NAME);

    BElement unpackedEle = BElement.ofBytes(bytes, AvroMultiSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

where ``Person`` is a avro generated class.

.. note:: you must register the schema class before use `avro` serialization format. Only `BReference` contains registered `schema` can be serialized/deserialized

Write out binary
================

To work with I/O, data should be written to an output stream. There are 2 ways to do that:

1. convert to byte[] using ``BElement.toBytes()`` then append that bytes to output stream.
2. write directly to output stream using ``BElement.writeBytes(outputStream)``.

The second way is highly recommended because it save one mem-copying and will make your code faster.
