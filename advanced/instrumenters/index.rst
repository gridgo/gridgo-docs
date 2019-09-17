Instrumenters
=============

Instrumentering is a mechanism to alter a class behavior without directly modifying it. There are two types of instrumenters in Gridgo: Execution Strategy Instrumenter and Producer Instrumenter.

Execution Strategy Instrumenters
--------------------------------

Execution strategy instrumenters are used with gateway processors to alter their behaviors. When you attach an instrumenter to a processor, all handling will be routed to the it instead of the processor. Some examples of instrumenters might be:

- Calculate the throughput and latency of a processor via Prometheus integration.
- Apply access control going through a processor.

There are several instrumenters already available for use, e.g:

- WrappedExecutionStrategyInstrumenter: Wrap several instrumenters into a single one. Wrapped instrumenters will be executed sequentially.
- Prometheus-integration instrumenters: Integrate with Prometheus functionality. Can be used to calculate throughput and latency of a processor.

Producer Instrumenters
----------------------

Producer instrumenters are used with producers to alter their behaviors. Some examples of producers instrumenters might be:

- Calculate the throughput and latency of a producer (e.g a JDBC producer)
- Add a cache layer to the producer
- Add a mockup layer to the producer
