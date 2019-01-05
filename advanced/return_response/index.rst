Returning responses to connectors
=================================

Many Connectors require responses or acknowledgements from Processors, e.g in a 
HTTP server, you need to send the response back to client, or in Kafka you need 
to send acknowledgement back to KafkaConnector, so it will commit the message. 
This is done using the `Deferred` object in `RoutingContext`

.. code-block:: java
    
    private void handleMessages(RoutingContext rc, GridgoContext gc) {
        try {
            // do some work to get the response
            // Gridgo favors asynchronous over synchronous, so your method shouldn't block
            rc.getDeferred().resolve(response);
        } catch (Exception exception) {
            // or reject the request with some exception
            rc.getDeferred().reject(exception);
        }
    }

.. note:: Only the first call to either `resolve()` or `reject()` will work.
          Subsequent calls will be ignored.
