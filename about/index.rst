Introduction
============

This page aims at providing basic principles and terminologies of Gridgo.
It is recommended if you are new to Gridgo.

About Gridgo
------------

Gridgo is a platform to create distributed systems easier with asynchronous I/O connectors 
and event driven programming. Gridgo handles the heavylift work of I/O and thread 
routing so you can focus on designing network topologies and implementing business 
logic.

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
    
