Using Prometheus instrumenters with Gridgo Boot
===============================================

This tutorial will help you get up and running with Gridgo Boot Prometheus instrumenters. It is recommended that you read the instrumenters basic first. What it'll do:

1. Create a Prometheus instrumenter to measure a processor throughput and latency.
2. Expose Prometheus metrics through a HTTP endpoint.
3. Utilize the metrics in Grafana dashboard.

Installation
------------

To start using Prometheus instrumenters, first you need to add it in your pom.xml

.. code-block:: xml

    <dependency>
        <groupId>io.gridgo</groupId>
        <artifactId>gridgo-extras-prometheus</artifactId>
        <version>${gridgo.version}</version>
    </dependency>

Create and register an instrumenter
-----------------------------------

The next step is to create and register the instrumenter in Gridgo Registry. This can be done using a `@RegistryInitializer` method, which is probably placed inside your initialization class (the one you pass to `GridgoApplication.run(...)`, usually Main class or Initializer)

.. code-block:: java

    @RegistryInitializer
    public static void initRegistry(Registry registry) {
        // create a Prometheus summary instrumenter, referring to Prometheus documentation for more
        var instrumenter = new PrometheusSummaryTimeInstrumenter("my_summary", "My Summary");
        // register it in our registry
        registry.register("myInstrumenter", instrumenter);
    }

Attach the registered instrumenter to the processor
---------------------------------------------------

Simply add a `@Instrumenter` to your processor class with name you registered earlier.

.. code-block:: java

    @Gateway("my_gateway")
    @Connector("${my_connector_endpoint}")
    @Instrumenter("myInstrumenter")
    public class MyProcessor extends AbstractProcessor {

        @Override
        public void process(RoutingContext rc, GridgoContext gc) {
            // handle the message as usual
        }
    }

Expose the metrics via a HTTP endpoint
--------------------------------------

Now everything is setup, but Prometheus is pull-based, so you need to expose metrics via a HTTP endpoint so that the Prometheus server can scrape it. The `MetricsProcessor` is already provided for your convenience. `my_metrics_endpoint` might be something like `vertx:http://0.0.0.0:8080/metrics`

.. code-block:: java

    @Gateway("metrics")
    @Connector("${my_metrics_endpoint}")
    public class MyMetricsProcessor extends MetricsProcessor {

        public MyMetricsProcessor() {
            super("my_metrics");
        }
    }

Visualize the metrics
---------------------

Ask your administrator or infrastructure team to scrape it using the endpoint you have specified earlier. Then you can query it use something like:

`rate(my_metrics_my_summary_time_count[30s])`: Return the average throughput of last 30 seconds
`rate(my_metrics_my_summary_time_count[30s]) / rate(my_metrics_my_summary_time_sum[30s])`: Return the average latency of last 30 seconds
