Introduction
============

This page aims at providing basic principles and terminologies of Gridgo.
It is recommended if you are new to Gridgo.

About Gridgo
------------

Gridgo is a platform to create distributed systems easier with asynchronous I/O 
connectors and event driven programming. Gridgo handles the heavylift work of I/O 
and thread routing so you can focus on designing network topologies and implementing 
business logic.

Gridgo Principles
-----------------

Everyone has principles, so does Gridgo. Gridgo follows and strongly recommends 
several principals, which are:

Fluent API
    Most Gridgo APIs are fluent. You can perform successive operations within
    a single method calls chain.

Asynchronous over Synchronous
    It is as straightforward as it sounds. Most of the operations in Gridgo are 
    asynchronous, even for remote-procedure calls. Asynchronous operations will
    give you a ``Promise``, which you can be notified when it is fulfilled or
    rejected. Some operations which need response from you will give you a ``Deferred``
    that you can fulfill or reject it. Your method also should not block.

Think topology over protocol
    Instead of focusing on the actual protocol, you should focus on designing the network
    or computing topology. All I/O operations are abstracted using ``Connector`` or 
    ``Gateway``.

Gridgo Components
-----------------

The main components of Gridgo are:

Connector
    This is the most basic abstraction level in Gridgo. It provides easy-to-use
    I/O connection (sending and receiving messages). Each type of connector is
    uniquely represented by an ``endpoint``. An example might be 
    `"kafka:mytopic?brokers=127.0.0.1"`. Connector consists of ``Producer`` and
    ``Consumer``

Producer
    The outgoing part of ``connector``. It allows you to send messages to the 
    remote service with `fire-and-forget` or `RPC` styles.

Consumer
    The incoming part of ``connector``. It allows you to subscribe and handling 
    messages. Consumer can be two-way, which means it can also send response back
    to the caller.

Gateway
    This is the bridge between ``Connector`` and application logic. It allows you
    to subscribe for messages from and send messages to the underlying connectors.
    Gateways are always accessed by name.

Application Context
    This is the highest level component, which will connect all other components,
    like opening gateways, resolving connectors, etc. You can create any number of
    contexts inside a single JVM process.
