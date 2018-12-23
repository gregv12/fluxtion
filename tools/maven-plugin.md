---
description: Invoking Fluxtion compiler from maven
---

# Maven plugin

## Introduction

Invoking Fluxtion as a standalone tool can have various difficulties attached to it. Wrapping the use of Fluxtion as a maven plugin has the following advantages:

* Fluxtion generation becomes part of the standard build process.
* No need to construct complex command line arguments.
* Input and output files reside in standard locations.
* Automation reduces developer effort and sources of error.

## Executing Fluxtion in maven

The tool steps a user should follow to execute the fluxtion event stream compiler via maven are :

* Create a maven project declaring Fluxtion libraries as dependencies.
* Write application classes representing nodes and events to fulfill [event processing](https://fluxtion.gitbook.io/docs/overview/child-2) requirement.
* Create meta-data describing the [construction of the execution graph](https://fluxtion.gitbook.io/docs/overview/graph-building-primitives).
* Add the fluxtion maven plugin to the build plugins, setting configuration.
* Run a mvn build.

The maven plugin is released to maven central and should be available without setting repositories.

### Fluxtion dependencies

Fluxtion has two libraries the user needs to declare in the dependency section, the libraries are described [here](fluxtion-tool.md#classpath). The builder library is not required in the final application, to remove the builder from the runtime set of dependencies specify the scope as provided. An example valid dependency section:

```markup
    <dependencies>
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-api</artifactId>
            <version>[FLUXTION VERSION]</version>
        </dependency>
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-builder</artifactId>
            <scope>provided</scope>
            <version>[FLUXTION VERSION]</version>
        </dependency>
    </dependencies>
```

### Develop SEP processor

Application event processing and graph description are covered in the [event processing](../overview/child-2/) and [graph building](../overview/graph-building-primitives/) section.

### Add Build plugins

Add the maven fluxtion plugin to the plugins section of the build. The plugin in by default binds to the compile phase of the maven build cycle.  Fluxtion will use if available the parameter meta-data supplied at compile time by javac, add the -parameters switch to the compiler plugin to add the meta-data.

A sample section plugin declaration in a project using Fluxtion:

```markup
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
                <version>${fluxtion.ver}</version>
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
                <executions>
            </plugin>
        <plugins>
    <build>
```

### Configure plugin

The plugin acts as a pass through to the [Fluxtion compiler](fluxtion-tool.md) supplying configuration to the tool as well. The configuration available for the fluxtion maven plugin can be printed to the console:

```text
d:\dvh\updated-reference-core>mvn help:describe -Dplugin=com.fluxtion:fluxtion-maven-plugin -Ddetail > mvn_plugin.out
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building fluxtion.example :: ref.core_UPDATED 1.5.4-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-help-plugin:2.2:describe (default-cli) @ reference_core ---
[INFO] com.fluxtion:fluxtion-maven-plugin:1.5.3

Name: fluxtion :: maven plugin
Description: A maven plugin integrating the fluxtion toolset with maven build
  cycle, inprocess
Group Id: com.fluxtion
Artifact Id: fluxtion-maven-plugin
Version: 1.5.3
Goal Prefix: fluxtion

This plugin has 1 goal:

fluxtion:generate
  Description: A mojo to wrap the invocation of the Fluxtion executable.
  Implementation: com.fluxtion.maven.FluxtionGeneratorMojo
  Language: java
  Bound to phase: compile

  Available parameters:

    assignNonPublicMembers (Default: false)
      User property: assignNonPublicMembers
      Override whether the generated static event processor supports reflection
      based assignment for initialisation.

    buildDirectory (Default: target/classes)
      User property: buildDirectory
      The output directory for build artifacts generated by fluxtion. Absolute
      paths are preceded with '/' otherwise the path relative to the project
      root directory

    className
      Required: true
      User property: className
      The simple class name of the generated static event processor.

    compileGenerated (Default: true)
      User property: compileGenerated
      Compile the source artifacts, placing the results in the build directory

    configClass
      User property: configClass
      The fully qualified name of SEPConfig class that fluxtion will use to
      build the static event processor

    filterNamingClass
      User property: filterNamingClass
      Fully qualified name of FilterDescriptionProducer implementation class,
      hat provides strategy to name filters in generated static event
      processor.

    formatSource (Default: true)
      User property: formatSource
      Format the generated source files.

    generateDebugPrep (Default: false)
      User property: generateDebugPrep
      Generate a debug version of the static event processor for use with the
      fluxtion graphical debugger tool.

    generateDescription (Default: true)
      User property: generateDescription
      Generate meta data for the generated static event processor. The meta
      includes a png and graphml describing the SEP.

    generateTestDecorator (Default: false)
      User property: generateTestDecorator
      Generate a test decorator for the static event processor

    http_proxyHost
      User property: http.proxyHost
      Set the http.proxyHost fluxtion will traverse to authenticate with the
      license server

    http_proxyPassword
      User property: http.proxyPassword
      Set the http.proxyUser fluxtion will traverse to authenticate with the
      license server

    http_proxyPort
      User property: http.proxyPort
      Set the http.proxyPort fluxtion will traverse to authenticate with the
      license server

    http_proxyUser
      User property: http.proxyUser
      Set the http.proxyUser fluxtion will traverse to authenticate with the
      license server

    ignoreErrors (Default: false)
      User property: ignoreErrors
      continue build even if fluxtion tool returns an error

    logDebug (Default: false)
      User property: logDebug
      Set log level to debug for fluxtion generation.

    nodeNamingClass
      User property: nodeNamingClass
      Fully qualified name of NodeNameProducer implementation class , that
      provides strategy to name nodes in generated static event processor.

    outputDirectory (Default: target/generated-sources/java)
      User property: outputDirectory
      The output directory for source artifacts generated by fluxtion. Absolute
      paths are preceded with '/' otherwise the path relative to the project
      root directory

    packageName
      Required: true
      User property: packageName
      The output package of the generated static event processor.

    resourcesOutputDirectory (Default: src/main/resources)
      User property: resourcesOutputDirectory
      The output directory for resources generated by fluxtion, such as a
      meta-data describing the static event processor. Absolute paths are
      preceded with '/' otherwise the path relative to the project root
      directory

    rootFactoryClass
      User property: rootFactoryClass
      The fully qualified name of a root NoceFactory class that will be used in
      conjunction of the yaml factory configuration to generate a static event
      processor.

    supportDirtyFiltering (Default: true)
      User property: supportDirtyFiltering
      Override whether the generated static event processor supports dirty
      filtering.

    templateDebugSep
      User property: templateDebugSep
      Override the velocity template file that is used by fluxtion to generate
      the debug static event processor

    templateSep
      User property: templateSep
      Override the velocity template file that is used by fluxtion to generate
      the static event processor

    yamlFactoryConfig
      User property: yamlFactoryConfig
      The yaml configuration that is used in conjunction with a root
      NoceFactory to generate a static event processor.


[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.718 s
[INFO] Finished at: 2018-12-23T15:52:05Z
[INFO] Final Memory: 11M/245M
[INFO] ------------------------------------------------------------------------

```

### Run build

running the build will invoke maven as part of the standard build cycle in the compile phase:

```text
d:\dvh\updated-reference-core>mvn compile
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building fluxtion.example :: ref.core_UPDATED 1.5.4-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ reference_core ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 26 resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.0:compile (default-compile) @ reference_core ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 60 source files to C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\target\classes
[INFO] 
[INFO] --- fluxtion-maven-plugin:1.5.4-SNAPSHOT:generate (build-injection) @ reference_core ---
[INFO] java -jar fluxtion.jar -outDirectory C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core/src/main/java -buildDirectory C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core/target/classes -outResDirectory C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core/src/main/resources -outPackage com.fluxtion.example.core.building.injection.generated -configClass com.fluxtion.example.core.building.injection.Builder -outClass SampleProcessor -buildClasses true -formatSource true -supportDirtyFiltering true -generateDebugPrep false -generateDescription true -assignPrivate false -cp C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\target\classes;C:\Users\gregp\.m2\repository\com\fluxtion\fluxtion-api\1.5.4-SNAPSHOT\fluxtion-api-1.5.4-SNAPSHOT.jar;C:\Users\gregp\.m2\repository\it\unimi\dsi\fastutil\7.0.7\fastutil-7.0.7.jar;C:\Users\gregp\.m2\repository\net\vidageek\mirror\1.6.1\mirror-1.6.1.jar;C:\Users\gregp\.m2\repository\com\fluxtion\fluxtion-builder\1.5.4-SNAPSHOT\fluxtion-builder-1.5.4-SNAPSHOT.jar
[INFO] Reflections took 191 ms to scan 13 urls, producing 684 keys and 3264 values 
[INFO] missing default construtor - attempting construction bypass
[INFO] png image generated:C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\src\main\resources\com\fluxtion\example\core\building\injection\generated\SampleProcessor.png
[INFO] Updated com.fluxtion.example.core.building.injection.generated.SampleProcessor in C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\target\classes
[INFO] 
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ reference_core ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\src\test\resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.0:testCompile (default-testCompile) @ reference_core ---
[INFO] Nothing to compile - all classes are up to date
[INFO] 
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ reference_core ---
[INFO] 
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ reference_core ---
[INFO] Building jar: C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\target\reference_core-1.5.4-SNAPSHOT.jar
[INFO] 
[INFO] --- maven-install-plugin:2.4:install (default-install) @ reference_core ---
[INFO] Installing C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\target\reference_core-1.5.4-SNAPSHOT.jar to C:\Users\gregp\.m2\repository\com\fluxtion\example\reference_core\1.5.4-SNAPSHOT\reference_core-1.5.4-SNAPSHOT.jar
[INFO] Installing C:\Users\gregp\development\projects\fluxtion\open-source\fluxtion-examples\updated-reference-core\pom.xml to C:\Users\gregp\.m2\repository\com\fluxtion\example\reference_core\1.5.4-SNAPSHOT\reference_core-1.5.4-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 8.643 s
[INFO] Finished at: 2018-12-22T18:42:16Z
[INFO] Final Memory: 48M/636M
[INFO] ------------------------------------------------------------------------
```





