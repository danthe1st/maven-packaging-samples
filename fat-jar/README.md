This is a sample project that is packaged as a fat JAR using the [Maven Assembly Plugin](https://maven.apache.org/plugins/maven-assembly-plugin/).

Doing so extracts all dependencies into one JAR which one can then run using the `java -jar` command.

### Advantages

- The application can be distributed as a single file which can be run using `java -jar`
- The distribution format is platform-independent.

### Disadvantages

- Using fat JARs requires the users to install a JDK or JRE (most vendors don't publish JREs any more).
- JPMS cannot be used with fat JARs.
- The application must be rebuilt in order to swap dependencies.
