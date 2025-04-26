This module demonstrates how one can package applications as directories containing the application and all dependencies in unpacked (exploded) form as files.

This version puts each artifact in its own directory but this can be configured differently.

### Advantages

- Using an exploded format works both with and without JPMS.
- This packaging method is platform-independent.
- As Java does not need to deal with compression, this approach can lead to faster startup times as opposed to using JAR files.

### Disadvantages

- This packaging method requires a JDK/JRE to run the application
- This approach requires distributing one file for each class or resource.
- The application cannot be run with a simple `java -jar` command.

### Description

Exploded means that the application is not packaged using JAR files but the classes are on the file system using the hierarchical package structure.
In this module, the dependencies are unpacked in subdirectories (one directory per artifact) of `target/app` by the `maven-dependency-plugin`. The `maven-resources-plugin` is then used in order to copy the classes of the application to `target/app/app`.

```
$ tree target/app/
target/app/
├── app
│   ├── io
│   │   └── github
│   │       └── danthe1st
│   │           └── mavenpackaging
│   │               └── exploded
│   │                   └── ExplodedMain.class
│   └── module-info.class
└── lib-0.0.1-SNAPSHOT
    ├── io
    │   └── github
    │       └── danthe1st
    │           └── mavenpackaging
    │               └── lib
    │                   └── LibClass.class
    ├── META-INF
    │   ├── MANIFEST.MF
    │   └── maven
    │       └── io.github.danthe1st.mavenpackaging
    │           └── lib
    │               ├── pom.properties
    │               └── pom.xml
    └── module-info.class

17 directories, 7 files
```

With this structure, the application can then be run using the following command:

```bash
java -p target/app -m packaging.exploded/io.github.danthe1st.mavenpackaging.exploded.ExplodedMain
```


#### Flat exploded format

In applications that do not make use of JPMS often copy the application and unpack all dependencies into one directory without using subdirectories for each artifact.
This structure is similar to [far JARs](../fat-jar) and does not work with modular applications because classes of different modules are mixed in the same directory structure.
In this example, this can be achieved by removing `<useSubDirectoryPerArtifact>true</useSubDirectoryPerArtifact>` and replacing `<outputDirectory>${project.build.directory}/app/app</outputDirectory>` with `<outputDirectory>${project.build.directory}/app</outputDirectory>` in the `pom.xml`. This would result in the following directory structure:

```
$ tree target/app
target/app/
├── io
│   └── github
│       └── danthe1st
│           └── mavenpackaging
│               ├── exploded
│               │   └── ExplodedMain.class
│               └── lib
│                   └── LibClass.class
├── META-INF
│   ├── MANIFEST.MF
│   └── maven
│       └── io.github.danthe1st.mavenpackaging
│           ├── exploded
│           │   ├── pom.properties
│           │   └── pom.xml
│           └── lib
│               ├── pom.properties
│               └── pom.xml
└── module-info.class

12 directories, 8 files
```

Such an application can be executed using the following command. As the classpath (`-cp`) is used instead of the modulepath (`-p`), the module descriptor is ignored.
```bash
java -cp target/app/ io.github.danthe1st.mavenpackaging.exploded.ExplodedMain
```