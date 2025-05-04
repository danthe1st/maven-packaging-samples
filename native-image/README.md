This is a sample project illustrating how a Java application can be packaged using `native-image` (with the `native-maven-plugin`).
Applications using `native-image` are compiled to native executables eliminating any unused code.

### Advantages

- The project can be distributed as a single file (using native code can result in additional files being necessary).
- No JDK/JRE is necessary to run such a project. Everything necessary is included in the native executable.
- `native-image` works both with and without JPMS.
- Compiling applications with `native-image` significantly reduces the memory overhead and startup/warmup time/time to peak performance.
- It is possible to [link the application at build-time](https://stackoverflow.com/a/79155800/10871900) ensuring that all used classes/methods/fields are actually present.

### Disadvantages

- This packaging method is platform-dependent.
- The current version of the `native-maven-plugin` puts all dependencies on the classpath so making use of JPMS requires additional configuration.
- If the application (or some of its dependencies) makes use of reflection, JNI or resources, it is necessary to [add metadata](https://www.graalvm.org/latest/reference-manual/native-image/metadata/). Otherwise the project cannot be run due to the relevant classes or resources being eliminated. Reflection metadata is available for many dependencies via the [graalvm-reflection-metadata](https://github.com/oracle/graalvm-reachability-metadata/) repository which is automatically picked up by the `native-maven-plugin`.
- Classpath scanning and custom class loading at runtime is not supported. This must be done at compile-time.
- Due to the restriction of needing (reflection) metadata, this is approach is complex to set up/configure for many bigger projects.

### Description

The `native:compile` goal creates a native executable in `target/<executable name>`, in this case `target/native-image`. This process can take a while, especially for bigger projects with many dependencies. This uses the `native-image` tool which is provided by GraalVM (but not available with other JDKs).

`native-image` is commonly used for compiling applications that run in memory-constrained environments, where memory is costly or where fast startup is critical. This is commonly the case in Cloud environments, where applications should be able to scale fast and deployment is billed by used resources.

While `native-image` can provide significant improvements with respect to startup and warmup time as well as memory consumption, it makes use of a closed-world assumption for doing that. It analyzes the program to figure out which parts are actually used, eliminates parts that are not used and compiles the application to a native binary. This limits some of the dynamic capabilities Java would otherwise provide.
Correctly setting up the configuration to correctly identify what is used and what is not can be a complicated process for nontrivial projects, especially when dependencies are used which do not support `native-image`.

One thing to note is that building native images can take a significant amount of time. Because of that, it is often better to not bind that process to the `package` phase and only build the image when actually building such an executable (by running `mvn native:compile` or `native:test`) when needed. It is also possible to use a Maven `<profile>` for this and activate it with `-Pnative` or similar.