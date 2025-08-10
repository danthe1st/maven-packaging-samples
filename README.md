# Maven packaging samples

This repository contains example projects that illustrate ways to package Java applications with Maven.

The sample projects include the [lib](./lib) dependency which is a "normal" `jar` artifact.

The following table outlines the differences between the packaging methods. The JPMS column shows whether JPMS (using a module-info.java) is supported and whether it is necessary to use JPMS.

|project                       |description                         |JPMS         |Java runtime|platform independent|single file|
|:-----------------------------|:-----------------------------------|:------------|:-----------|:-------------------|:----------|
|[multijar](./multijar)        |multiple JAR files                  |supported    |required    |yes                 |no         |
|[jlink](./jlink)              |bundles minimal JRE with application|required     |included    |no                  |no         |
|[jpackage](./jpackage)        |installer                           |supported    |included    |no                  |yes        |
|[fat-jar](./fat-jar)          |single JAR with all dependencies    |not supported|required    |yes                 |yes        |
|[native-image](./native-image)|single native OS executable         |supported\*  |not needed  |no                  |yes        |
|[exploded](./exploded)        |unpacked classes                    |supported    |required    |yes                 |no         |
|[jar-in-jar](./jar-in-jar)    |nested JAR files                    |supported\*  |required    |yes                 |yes        |

\* While it is possible to use JPMS with both `native-image` and nested JARs (jar-in-jar), doing so requires additional configuration and is not included in this repository.

### Building

The projects can be built using `mvn package`.

The build of the [native-image](./native-image) is disabled by default as it requires a GraalVM installation. It is enabled if it can detect a GraalVM installation automatically. It can be manually enabled using the `-Pbuild-native-image` flag (e.g. `mvn package -Pbuild-native-image`).

### Libraries

The packaging methods shown here **should not be used for packaging libraries**.  
Libraries should be packaged as thin JARs (which is the default in Maven, see [lib](./lib)) and distributed using Maven repositories. Packaging libraries using fat JARs introduces duplicate dependencies, split packages and dependency conflicts. Providing an _additional_ download of a library using a fat JAR for people that want to use the library in Java projects without using a build tool is fine but this fat JAR should not be the main artifact that is published in a Maven repository. The jlink and native-image packaging methods are applicable for libraries.

### Notable alternatives not included

This repository does not include include the following packaging methods for applications:
- Framework specific packaging: Frameworks like Spring Boot sometimes have their own ways of packaging applications.