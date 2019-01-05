Multiple connectors per gateway
===============================

It is possible to attach multiple connectors to a single Gateway. Doing so will make incoming messages from all attached Connectors to be routed to the Processors. One more interesting thing is that when you send messages to Gateway (using either `send()`, `sendWithAck()`, `call()` or `callAndPush()`), the messages will also be multiplexed to all Connectors.

So what is the response if you make RPC calls to a Gateway having multiple Connectors? Well, Gridgo allows you choose the strategy to compose the response, using `ProducerTemplate`. There are 3 built-in types of `ProducerTemplate`:

- **SingleProducerTemplate**: which will keep the first Connector response and discard all others, this is the default template
- **JoinProducerTemplate**: which will merge all responses into a single `MultipartMessage`
- **MatchingProducerTemplate**: similar to `JoinProducerTemplate`, but allows you to use a `Predicate` to filter what Connector to be called. Responses are also merged into a single `MultipartMessage`
