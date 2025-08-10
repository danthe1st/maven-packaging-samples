This is a sample project demonstrating how a Java application can be published as a JAR with dependencies being located in a subdirectory.
The `MANIFEST.MF` of the JAR references that directory to allow it being executed with `java -jar` (without needing to specify any dependencies/classpath/modulepath).

### Advantages

- Using multiple JARs works both with and without JPMS.
- This packaging method is platform-independent.
- All dependency JARs are preserved as-is including signatures and metadata.

### Disadvantages

- This packaging method requires a JDK/JRE to run the application
- This packaging method requires distributing multiple files.

### Description

The `dependency:copy-dependencies` goal copies the dependencies necessary to run the application into the `target/dependency` directory and the `maven-jar-plugin` is configured to create a JAR file with a `META-INF/MANIFEST.MF` referencing these dependencies in a `dependency/` directory relative to the JAR. Such a directory is commonly named `lib/`.
```
Manifest-Version: 1.0
Created-By: Maven JAR Plugin 3.4.2
Build-Jdk-Spec: 24
Class-Path: dependency/lib-0.0.1-SNAPSHOT.jar
Main-Class: io.github.danthe1st.mavenpackaging.multijar.MultijarMain
```
Running the application with `java -jar` picks up that `Class-Path` entry and adds these dependencies to the classpath to make then available at runtime.

While running this packaged application via `java -jar` is possible, this results in the JARs being put on the classpath which ignores the module descriptors. The following command can be used to run the application in a modular way:
```bash
java -p target/:target/dependency/ -m packaging.multijar
```

Distributing applications as multiple JARs (possibly in a `.zip` or `.tar.gz` file) can be useful for applications making use of JPMS modules that require a full JDK (e.g. for a plugin system) or are deployed in environments that have a JDK/JRE already. This deployment is the closest to how applications are typically executed during development (with the JAR files being located in the Maven repository and resolved using build tools and IDEs).