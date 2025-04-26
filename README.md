# Maven packaging samples

This repository contains example projects that illustrate ways to package Java applications with Maven.

The sample projects include the [lib](./lib) dependency which is a "normal" `jar` artifact.

The following table outlines the differences between the packaging methods. The JPMS column shows whether JPMS (using a module-info.java) is supported and whether it is necessary to use JPMS.

|project                       |description                         |JPMS         |Java runtime|platform independent|
|:-----------------------------|:-----------------------------------|:------------|:-----------|:-------------------|
|[multijar](./multijar)        |multiple JAR files                  |supported    |required    |yes                 |
|[jlink](./jlink)              |bundles minimal JRE with application|required     |included    |no                  |
|[fat-jar](./fat-jar)          |single JAR with all dependencies    |not supported|required    |yes                 |
|[native-image](./native-image)|single native OS executable         |supported\*  |not needed  |no                  |
|[exploded](./exploded)        |unpacked classes                    |supported    |required    |yes                 |

\* While native-image supports JPMS, using it with the `native-maven-plugin` requires additional configuration.

### Building

The projects can be built using `mvn package`.

The build of the [native-image](./native-image) is disabled by default as it requires a GraalVM installation. It is enabled if it can detect a GraalVM installation automatically. It can be manually enabled using the `-Pbuild-native-image` flag (e.g. `mvn package -Pbuild-native-image`).

### Libraries

The packaging methods shown here **should not be used for packaging libraries**.  
Libraries should be packaged as thin JARs (which is the default in Maven, see [lib](./lib)) and distributed using Maven repositories. Packaging libraries using fat JARs introduces duplicate dependencies, split packages and dependency conflicts. Providing an _additional_ download of a library using a fat JAR for people that want to use the library in Java projects without using a build tool is fine but this fat JAR should not be the main artifact that is published in a Maven repository. The jlink and native-image packaging methods are applicable for libraries.

### Notable alternatives not included

This repository does not include include the following packaging methods for applications:
- `jpackage`: This is similar to jlink but creates a single executable which installs a minimal JRE and the application
- nested JAR files: It is possible to distribute applications as a single JAR file containing the JAR files of dependencies instead of extracting them. This combines the advantages of [having the dependencies in a subdirectory](./multijar) and [fat JARs](./fat-jar) (including the option to use JPMS). 
- Framework specific packaging: Frameworks like Spring Boot sometimes have their own ways of packaging applications.