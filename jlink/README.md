This is a sample project using the `jlink` tool to package Maven applications.
The `jlink` tool uses the module descriptor (`module-info.java`) to figure out what JDK modules are required for the application and builds a runtime consisting only of the necessary modules.

### Advantages

- Applications packaged with jlink do not need an external JDK/JRE. Everything necessary is included.
- Such applications can make full use of JPMS.

### Disadvantages

- This packaging format is platform-dependent.
- The application must have a valid module descriptor (`module-info.java`).
- Using jlink results in multiple files needing to be distributed.

### Description

The `jlink` tool can create runtime images containing only the modules declared in the module descriptor (`module-info.java`) of the application and its dependencies. The generated runtime image is based on the JDK it has been built with.

```
$ ./target/maven-jlink/default/bin/java --list-modules
java.base@24
packaging.jlink
packaging.lib@0.0.1-SNAPSHOT
```

This packaging format is commonly used for (containerized) server applications which do not require a full JDK to run. In many cases, a runtime image created with `jlink` is preferable over requiring users to install a JRE.

In this project, the `jlink` tool is configured to include a launcher named `run` that runs the application:
```
$ ./target/maven-jlink/default/bin/run 
Hello World
```

The `maven-jlink-plugin` adds the `jlink` packaging which creates such a runtime image.