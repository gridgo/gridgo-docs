Transaction in Processors
=========================

Gridgo supports asynchronous transaction through messaging. If a gateway is attached with a connector which supports Transaction (more on this later), a Processor can use it to call requests, commit or rollback the transaction.

Transactions are done through `io.gridgo.connector.support.transaction.Transaction` object. To make gateway agnostic of transaction, the creation of the Transaction object is done via Message (see Behind the scene section). A Processor can also implements `TransactionProcessor` interface, which provides utility methods for handling transactions.

Creating and processing transactions
------------------------------------

There are several ways to create and process transactions. All of the following code are valid and are exactly the same. `createTransaction` and `withTransaction` methods are provided by `TransactionProcessor`

1. Create, commit and rollback transaction manually

.. code-block:: java

    createTransaction(gateway).done(transaction -> {
        // use the Transaction object to query and commit/rollback manually
        transaction.callAny("insert into some_table values(..)")
                   .pipeDone(result -> doSomethingWithResult())
                   .done(result -> transaction.commit())
                   .fail(ex -> transaction.rollback());
    }).fail(ex -> logger.error("Cannot create transaction", ex));

2. Create transaction automatically but commit/rollback manually

.. code-block:: java 
    
    // create transaction automatically but still commit/rollback manually
    withTransaction(gateway, transaction -> {
        transaction.callAny("insert into some_table values(..)")
                   .pipeDone(result -> doSomethingWithResult())
                   .done(result -> transaction.commit())
                   .fail(ex -> transaction.rollback())
    }).fail(ex -> logger.error("Cannot create transaction", ex));    

3. Create transaction manually and use provided deferred to commit/rollback

.. code-block:: java 
    
    // create transaction automatically and use provided deferred to commit/rollback
    // the transaction will be committed when deferred is resolved, and rolled back
    // when it is rejected
    withTransaction(gateway, (transaction, deferred) -> {
        transaction.callAny("insert into some_table values(..)")
                   .pipeDone(result -> doSomethingWithResult())
                   .forward(deferred);
    }).fail(ex -> logger.error("Cannot create transaction", ex));    

4. Create transaction automatically and return a promise to commit/rollback

.. code-block:: java 
    
    // create transaction automatically and return a promise to let Gridgo knows
    // when to commit/rollback the transaction. 
    withTransaction(gateway, transaction -> {
        return transaction.callAny("insert into some_table values(..)")
                          .pipeDone(result -> doSomethingWithResult());
    }).fail(ex -> logger.error("Cannot create transaction", ex));    

