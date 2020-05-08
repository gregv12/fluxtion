---
description: Reference examples accompanying user documentation
---

# Reference

## Introduction

The set of examples that accompany the [documentation](../concepts/overview/) are located in the main fluxtion code repo [here](https://github.com/v12technology/fluxtion/tree/master/examples/documentation-examples).

to build the examples regenerating the Fluxtion outputs use the following maven command:

```text
cd examples/documentation-examples
mvn install
```

## Project structure

The package structure of the example follows that of the documentation.

| document section | Package |
| :--- | :--- |
| [Event processing](../concepts/overview/child-2/) | [com.fluxtion.example.core.events ](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events) |
| [Graph building](../concepts/overview/graph-building-primitives/) | [com.fluxtion.example.core.building](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/building) |
| [Dependency injection](../concepts/overview/dependency-container/) | [com.fluxtion.example.core.dependencyinjection](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/dependencyinjection) |
| [Auditing](../concepts/overview/auditing.md) | [com.fluxtion.example.core.audit ](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/audit) |
| [Generation output control](../concepts/overview/generation/) | [com.fluxtion.example.core.outstyle](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/outstyle) |

### Maven pom

In the root of the project is the main maven [pom file](https://github.com/v12technology/fluxtion/blob/develop/examples/documentation-examples/pom.xml). The pom builds all the examples generating a SEP for each example, a section of the pom file is shown below:

```markup
        <profile>
            <id>build-examples</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.8.0</version>
                        <configuration>
                            <compilerArgs>
                                <arg>-parameters</arg>
                            </compilerArgs>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>com.fluxtion</groupId>
                        <artifactId>fluxtion-maven-plugin</artifactId>
                        <version>${project.version}</version>
                        <executions>
                            <execution>
                                <id>filtering</id>
                                <goals>
                                    <goal>generate</goal>
                                </goals>
                                <configuration>
                                    <configClass>com.fluxtion.example.core.events.filtering.Builder</configClass>
                                    <packageName>com.fluxtion.example.core.events.filtering.generated</packageName>
                                    <className>SampleProcessor</className>
                                    <outputDirectory>src/main/java</outputDirectory>
                                    <generateDescription>false</generateDescription>
                                </configuration>
                            </execution>
                            <execution>
                                <id>single-handler</id>
                                <goals>
                                    <goal>generate</goal>
                                </goals>
                                <configuration>
                                    <configClass>com.fluxtion.example.core.events.singlehandler.Builder</configClass>
                                    <packageName>com.fluxtion.example.core.events.singlehandler.generated</packageName>
                                    <className>SampleProcessor</className>
                                    <outputDirectory>src/main/java</outputDirectory>
                                </configuration>
                            </execution>
                            .....
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
```

#### Notes

* A profile "build-examples" contains the Fluxtion plugin execution. This profile is enabled by default.
* The compiler plugin is specified with the parameters option.
* Multiple Fluxtion executions are defined, one for each example
* Most examples use an imperative form specifying the config class..

