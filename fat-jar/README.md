This is a sample project that is packaged as a fat JAR using the [Maven Assembly Plugin](https://maven.apache.org/plugins/maven-assembly-plugin/).

Doing so extracts all dependencies into one JAR which one can then run using the `java -jar` command.

### Advantages

- The application can be distributed as a single file which can be run using `java -jar`
- The distribution format is platform-independent.

### Disadvantages

- Using fat JARs requires the users to install a JDK or JRE (most vendors don't publish JREs any more).
- JPMS cannot be used with fat JARs.
- The application must be rebuilt in order to swap dependencies.

### Description

Fat JARs or Uber-JARs include dependencies along with the compiled code.
The `assembly:single` goal is configured to use the built-in `jar-with-dependencies` descriptor to [build an executable JAR](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html#creating-an-executable-jar).

Fat JARs are often used when the developers want to distribute their application as a single file which is the same for all operating systems/platforms.

The resulting JAR file has the following structure on this project:
```
$ jar tf target/fat-jar-0.0.1-SNAPSHOT-jar-with-dependencies.jar | tree --fromfile /dev/stdin | tail -n +2
├── io
│   └── github
│       └── danthe1st
│           └── mavenpackaging
│               ├── fatjar
│               │   └── FatJarMain.class
│               └── lib
│                   └── LibClass.class
├── META-INF
│   ├── MANIFEST.MF
│   └── maven
│       └── io.github.danthe1st.mavenpackaging
│           ├── fat-jar
│           │   ├── pom.properties
│           │   └── pom.xml
│           └── lib
│               ├── pom.properties
│               └── pom.xml
└── module-info.class

12 directories, 8 files
```

While this structure contains a `module-info.class` file, this file is coming from the `lib` dependency and does not consider the requirements of the `fat-jar` module.
```java
$ javap module-info.class
module packaging.lib@0.0.1-SNAPSHOT {
  requires java.base;
  exports io.github.danthe1st.mavenpackaging.lib;
}
```
While this can be handled in different ways (e.g. omitting the `module-info.class`), it is not possible to create a single JAR file consisting of multiple modules without using a custom `ClassLoader`.
This is not an issue when running the application with `java -jar` because that command puts the application on the classpath which ignores the module descriptor.