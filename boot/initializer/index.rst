Gridgo Boot Initializers
=================================

Registry Initializers
---------------------

By default, Gridgo Boot will create a `MultiSourceRegistry` with the following child registries in that order:

- `SystemPropertyRegistry`: Using Java system properties.

- `SystemEnvRegistry`: Using system environment variables.

- Profile-specific `PropertiesFileRegistry`: Based on the `defaultProfile` settings or the Java system property `gridgo.profile` or the environment variable `gridgo_profile`.

- Application `PropertiesFileRegistry`: Using `application.properties`

If you want to register custom beans into the registry, add a public static method with `@RegistryInitializer` in the Main class:

.. code-block:: java

    @RegistryInitializer
    public static void initRegistry(Registry registry) {
        // Register your custom beans here
        registry.register("someBean", new SomeBean());
    }


If you want to add more registries, add a public static method with `@RegistryFactory` in the Main class:

.. code-block:: java

    @RegistryFactory
    public static Registry createRegistry(Registry registry) {
        return new MyRegistry();
    }

The added registries will take precedence over the default ones.
