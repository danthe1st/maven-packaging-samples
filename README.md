# Maven packaging samples

This repository contains example projects that illustrate ways to package Java applications with Maven.

The [lib](lib) project contains a dependency which is used by the other projects.

|project|description|JPMS|requires JRE|platform independent|
|multijar|multiple JAR files|supported|yes|yes|
|jlink|bundles minimal JRE with application|required|no|no|
|fat-jar|single JAR with all dependencies|not supported|yes|yes|
|native-image|single native OS executable|supported|no|no|