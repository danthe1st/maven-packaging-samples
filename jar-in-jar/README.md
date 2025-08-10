This sample project shows how Java applications can be packaged in a single JAR while preserving the individual dependency JARs.  
This packaging method is used when exporting a runnable JAR in Eclipse with the option "Package required libraries into generated JAR".

This specific project is more about demonstrating how this can be done as opposed to an example following best-practices in any way. Maven does not provide logic or first-party plugins specifically for this packaging method but it provides the necessary primitives to make use of it.

### Advantages

- The application can be distributed as a single file which can be run using `java -jar`.
- The packaged application is platform-independent.
- All dependency JARs are preserved as-is including signatures and metadata.
- It is possible to make use of JPMS but doing so required extra effort (not in this sample).

### Disadvantages

- The setup is fairly complex.
- Using this packaging method requires the use of a custom `ClassLoader`.
- The application classes are not directly included in the JAR file.
- The service loader API might not find services in nested JARs.
- Classes are no longer loaded by the default `ClassLoader` provided by the JDK which may result in issues with applications relying on that.

### Description

[`URLClassLoader`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/URLClassLoader.html) is capable of loading classes and resources from arbitrary locations at runtime. Among other uses, this allows copying JAR files inside the application JAR and loading the application and its dependencies from these nested JARs at runtime.

This sample project uses the [jar in jar loader from Eclipse JDT](https://github.com/eclipse-jdt/eclipse.jdt.ui/tree/master/org.eclipse.jdt.ui/jar%20in%20jar%20loader) to load the classes from the nested JARs at runtime.

The resulting JAR file has the following format:
```
|- META-INF/
|  |- MANIFEST.MF
|- lib/
|- org/eclipse/jdt/internal/jarinjarloader/
```

- The `META-INF/MANIFEST.MF` specifies the main class being `org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader` instead of the name of the main class of the application. In addition to the `Main-Class`, it contains the following attributes:
  - `Rsrc-Class-Path` contains a list (separated by spaces) of the (nested) JAR files to load including both the application and the dependencies.
  - `Rsrc-Main-Class` is set to the actual main class of the application.
- The `lib` directory contains the JARs to load.
- The code responsible for loading the application and dependencies (`org/eclipse/jdt/internal/jarinjarloader/`) is included as class files in the directories corresponding to their package just as it would be the case with "normal" JAR files.

The build process of this sample works as follows:
- The runtime classpath (the `target/classes` directory and the dependency JARs) is obtained using the `maven-antrun-plugin` and stored in the variable `runtime_classpath`.
- This variable is transformed into the format expected by the JAR in JAR loader by keeping only the file names, prepending them with `lib/` and separating them by spaces. This is done using the `build-helper-maven-plugin`.
  - First, the `runtime_classpath` variable is stripped of file names while only preserving JAR files (the `target/classes` directory is stripped as well) and `: lib/` is used to separate the entries. The `:` character is necessary because leading (and trailing) spaces are removed when the pom.xml is parsed.
  - All `:` characters are removed.
- The [`jar-in-jar-loader.zip`](https://github.com/eclipse-jdt/eclipse.jdt.ui/blob/master/org.eclipse.jdt.ui/jar-in-jar-loader.zip) is downloaded (with the `wagon-maven-plugin`) from the GitHub repository
- That zip file is extracted to `target/extract` using the `maven-antrun-plugin`
- The dependencies are copied to `target/extract/lib` using the `maven-dependency-plugin`.
- The "normal" JAR file of the project is also created inside `target/extract/lib`.
- The final JAR file is created using the contents of the `target/extract` directory.
  - The `Main-Class` manifest attribute is set to `org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader`.
  - The `Rsrc-Main-Class` manifest attribute is set to `io.github.danthe1st.mavenpackaging.multijar.JarInJarMain`.
  - The `Rsrc-Class-Path` manifest attribute is set to the path of the application JAR (within the `lib/` folder of final JAR) and the prepared runtime classpath (`runtime_classpath.spaces`)

