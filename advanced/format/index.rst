Thread-safe
==========
By default, all BElement created by default static methods ``of[...]()`` from BElement (and its sub-interfaces) are always not thread-safe.

To create a BElement which is thread-safe, use ``BObject.withHolder(<holder_map>)`` or ``BArray.withHolder(<holder_list>)``. Where input value should be thread-safe.

Pluggable serialization
=============================

By design, every serialization format defined by a `BSerializer`, include all the build-in kinds.

For example, by a global way you can use: ``BElement.fromBytes(bytes, "json")`` where "json" indicate the `BSerializer` instance named "json" will be used to deserialize bytes

To write you own serializer, create a class which implements `BSerializer` and annotate it by ``@BSerializationPlugin(name)``. After create, register it with `BSerializerRegistry` by calling:

.. code-block:: java

    BFactory.DEFAULT.getSerializerRegistry().scan(<package_name>);

or

.. code-block:: java

    BFactory.DEFAULT.getSerializerRegistry(<name>, <BSerializer instance>);

Default binary serializer
=========================

Bean using a system property named ``gridgo.bean.serializer.binary.default`` to take default binary serializer name, if it's unset, ``msgpack`` will be used.

Default serializer will be used in ``toBytes()``, ``writeBytes(...)`` and ``fromBytes(...)`` methods.

Schema and schema-less
=============================

Schema
------
There are a lot of binary serialization format which based on `schema` - generally understand as a value object.

To serialize an object as in schema serialization format, object's type must be registered with serializer.

In case multi schemas used in a single serializer, it should be prepended by a `type` header (commonly using 4 bytes integer);

There are 2 abstract interfaces for `schema serialization`: ``io.gridgo.bean.serialization.MultiSchemaSerializer<S>`` and ``io.gridgo.bean.serialization.SingleSchemaSerializer<S>``

Schema-less
-----------
`JSON`, `XML`, `msgpack` mentioned as built-in format are schema-less, mean it doesn't need to pre-define the schema class.

Pre-support serialization
=============================

Gson
----

By default, `json` serializer using ``json-smart`` lib, but if you have your own reason to use ``gson``, you can do it by add maven dependency:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-gson</artifactId>
        <version>x.x.x</version>
    </dependency>

Protobuf
--------
Protobuf is a popularly serialization format. Gridgo-bean already support it by a dependency:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-protobuf</artifactId>
        <version>x.x.x</version>
    </dependency>

`gridgo-bean-protobuf support protobuf in 2 mode:

- Single schema:
.. code-block:: java

    ProtobufSingleSchemaSerializer protobufSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(ProtobufSingleSchemaSerializer.NAME);
    protobufSerializer.setSchema(Person.class);
    Person p = Person.newBuilder().setName("Bach Hoang Nguyen").setAge(30).build();
    BElement ele = BElement.ofAny(p);
    byte[] bytes = ele.toBytes(ProtobufSingleSchemaSerializer.NAME);
    BElement unpackedEle = BElement.ofBytes(bytes, ProtobufSingleSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

- Multi schema:
.. code-block:: java

    ProtobufMultiSchemaSerializer protobufSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(ProtobufMultiSchemaSerializer.NAME);
    protobufSerializer.registerSchema(Person.class, 1);
    Person p = Person.newBuilder().setName("Bach Hoang Nguyen").setAge(30).build();
    BElement ele = BElement.ofAny(p);
    byte[] bytes = ele.toBytes(ProtobufMultiSchemaSerializer.NAME);
    BElement unpackedEle = BElement.ofBytes(bytes, ProtobufMultiSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

where ``Person`` is a protobuf generated class.

** you must register the schema class before use `protobuf` serialization format

Avro
----

Like protobuf, Avro is also a widely-use serialization format. To use it, add below lines to your pom.xml:

.. code::

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-bean-avro</artifactId>
        <version>x.x.x</version>
    </dependency>

Avro serialzier also support 2 modes:

- Single schema:
.. code-block:: java

    AvroSingleSchemaSerializer avroSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(AvroSingleSchemaSerializer.NAME);
    avroSerializer.setSchema(Person.class);

    Person p = Person.newBuilder().setName("Bach Hoang Nguyen").setAge(30).build();
    byte[] bytes = BElement.ofAny(p).toBytes(AvroSingleSchemaSerializer.NAME);
    System.out.println(ByteArrayUtils.toHex(bytes, "0x"));

    BElement unpackedEle = BElement.ofBytes(bytes, AvroSingleSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

- Multi schema:
.. code-block:: java

    AvroMultiSchemaSerializer avroSerializer = BFactory.DEFAULT.getSerializerRegistry().lookup(AvroMultiSchemaSerializer.NAME);
    avroSerializer.registerSchema(Person.class, 1);

    Person p = Person.newBuilder().setName("Bach Hoang Nguyen").setAge(30).build();
    byte[] bytes = BElement.ofAny(p).toBytes(AvroMultiSchemaSerializer.NAME);
    System.out.println(ByteArrayUtils.toHex(bytes, "0x"));

    BElement unpackedEle = BElement.ofBytes(bytes, AvroMultiSchemaSerializer.NAME);
    Person p2 = unpackedEle.asReference().getReference();

    assertEquals(p, p2);

where ``Person`` is a avro generated class.

** you must register the schema class before use `avro` serialization format

Write out binary
======================
To work with I/O, data should be write to an output stream. There are 2 ways to do that:

1. convert to byte[] using ``BElement.toBytes()`` then append that bytes to output stream.
2. write directly to output stream using ``BElement.writeBytes(outputStream)``.

The second way is highly recommended because it save 1 times mem-copying and will make your code faster.
