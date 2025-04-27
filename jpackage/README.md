This module shows how to use jpackage to create an installer for a runtime image including the application and its dependencies

### Advantages

- Applications packaged with jlink do not need an external JDK/JRE. Everything necessary is included.
- Such applications can make full use of JPMS.
- The application can be distributed as a single file.

### Disadvantages

- This packaging format is platform-dependent.
- It is necessary run an installer before the application before using it.

### Description

The `jpackage` tool is similar to `jlink` as in it creates a runtime image containing the necessary modules in order to run an application. As with `jlink`, the required modules are determined based on the module descriptors.
`jpackage` can create `.deb` installers on Linux, `.msi`/`.exe` installers on Windows and `.dmg` installers on MacOS.
This approach is commonly used for applications distributed towards end-users (e.g. desktop applications) that should have a simple installation process but rarely used for server applications as requiring an installation procedure is an unnecessary step for these applications.

This module uses a third-party Maven plugin (`org.panteleyev:jpackage-maven-plugin`) to run `jpackage` after copying the dependencies to `target/dependency` using the `maven-dependency-plugin`.

#### Notes

On Windows, it is necessary to install [WiX toolset](https://github.com/wixtoolset/wix) before packaging applications using this approach. Furthermore, the console (`System.out`/`System.err`/`System.in`) is not available in Windows unless `--win-console` is specified.

MacOS requires strictly positive three-digit version numbers. Versions like `1.0.0-SNAPSHOT` or `0.0.1` are not allowed.