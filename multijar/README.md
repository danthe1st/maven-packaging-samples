This is a sample project demonstrating how a Java application can be published as a JAR with dependencies being located in a subdirectory.
The `MANIFEST.MF` of the JAR references that directory to allow it being executed with `java -jar` (without needing to specify any dependencies/classpath/modulepath).

### Advantages

- Using multiple JARs works both with and without JPMS.
- This packaging method is platform-independent.

### Disadvantages

- This packaging method requires a JDK/JRE to run the application
- This packaging method requires distributing multiple files.