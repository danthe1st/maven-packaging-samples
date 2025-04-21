This is a sample project using the `jlink` tool to package Maven applications.
The `jlink` tool uses the module descriptor (`module-info.java`) to figure out what JDK modules are required for the application and builds a runtime consisting only of the necessary modules.

### Advantages

- Applications packaged with jlink do not need an external JDK/JRE. Everything necessary is included.
- Such applications can make full use of JPMS.

### Disadvantages

- This packaging format is platform-dependent.
- The application must have a valid module descriptor (`module-info.java`).
- Using jlink results in multiple files needing to be distributed.