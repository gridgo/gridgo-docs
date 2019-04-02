Bean Basics
===========

Beans are the abstract data structure of Gridgo aim to transparent data format and make easier to sending via network.

The hierachy as below:
```
    BElement ------------------------------------------------------ AbstractBElement
        |                                                                  |
        |                                                    +-------------+------------+
        |                                                    |             |            |
        +-- BValue ----------------------------------- MutableBValue       |            |
        |                                                                  |            |
        +-- BReference ------------------------------------------- MutableBReference    |
        |                                                                               |
        +-- BContainer -------------------------------------------------------- AbstractBContainer
                |                                                                       |
                |                                                           +-----------+-----------+
                |                                                           |                       |
                +-- BObject ---------------------------------------- AbstractBObject                |
                |       |                                                   |                       |
                |       +-- WrappedBObject ----+                +-----------+----------+            |
                |       |                      |                |                      |            |
                |       +-- ImmutableBObject --+------ WrappedImmutableBObject    MutableBObject    |
                |                                                                                   |
                +-- BArray ------------------------------------------------------------------ AbstractBArray
                        |                                                                           |
                        +-- WrappedBArray -----+                                        +-----------+-----------+
                        |                      |                                        |                       |
                        +-- ImmutableBArray ---+---------------------------- WrappedImmutableBArray        MutableBArray
```
