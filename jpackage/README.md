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
Alternatively, it is possible to use the [`exec-maven-plugin`](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) to create such an installer with a configuration similar to the following:
```xml
			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>exec-maven-plugin</artifactId>
				<version>3.5.0</version>
				<executions>
					<execution>
						<id>jpackage</id>
						<phase>package</phase>
						<goals>
							<goal>exec</goal>
						</goals>
						<configuration>
							<executable>jpackage</executable>
							<arguments>
								<argument>--module</argument>
								<argument>packaging.jpackage/io.github.danthe1st.mavenpackaging.jpackage.JpackageMain</argument>
								
								<argument>--name</argument>
								<argument>myapp</argument>
								
								<argument>--app-version</argument>
								<argument>1.0.0</argument>
								
								<argument>--module-path</argument>
								<classpath/>
								
								<argument>--dest</argument>
								<argument>${project.build.directory}/package</argument>
							</arguments>
						</configuration>
					</execution>
				</executions>
			</plugin>
```

#### Notes

On Windows, it is necessary to install [WiX toolset](https://github.com/wixtoolset/wix) before packaging applications using this approach. Furthermore, the console (`System.out`/`System.err`/`System.in`) is not available in Windows unless `--win-console` is specified.

MacOS requires strictly positive three-digit version numbers. Versions like `1.0.0-SNAPSHOT` or `0.0.1` are not allowed.

[Maven profiles](https://maven.apache.org/guides/introduction/introduction-to-profiles.html) can be used to configure OS-specific options. If the `exec-maven-plugin` is used, this can be used in conjunction with [`combine.children="append"`](https://maven.apache.org/pom.html#advanced_configuration_inheritance) to ensure the arguments are appended.