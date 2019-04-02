Advanced serialization format
=============================


Pluggable
~~~~~~~~~

By design, every serialization format defined by a BSerializer`, include all the build-in kinds.

For example, by a global way you can use: ``BElement.fromBytes(bytes, "json")`` where "json" indicate the `BSerializer` instance named "json" will be used to deserialize bytes

To write you own serializer, create a class which implements `BSerializer` and annotate it by ``@BSerializationPlugin(name)``. After create, register it with `BSerializerRegistry` by calling:

.. code-block:: java

    BFactory.DEFAULT.getSerializerRegistry().scan(<package_name>);
