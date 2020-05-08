---
description: Usage of the Fluxtion compiler described
---

# Fluxtion compiler

## Introduction

At the heart of Fluxtion lies the Fluxtion generator. An [executable jar](https://github.com/v12technology/fluxtion/tree/master/generator/dist) version of the generator is located in the dist directory of the generator project, that clients can download and use in their own projects

## Executing Fluxtion event stream compiler

The steps a user should follow to execute the fluxtion event stream compiler are:

* Write application classes representing nodes and events to fulfill [event processing](../../concepts/overview/child-2/) requirement.
* Create meta-data describing the[ construction of the execution graph](../../concepts/overview/graph-building-primitives/).
* Invoke the [fluxtion.jar](https://github.com/v12technology/fluxtion/blob/master/generator/dist/fluxtion.jar) on the command line with a set of parameters

{% hint style="info" %}
Constructing parameters for the first time can be complicated. The Fluxtion [maven plugin](maven-plugin.md) will print to console the equivalent command line to invoke Fluxtion generator directly.
{% endhint %}

## Classpath

Whichever construction strategy is used for compilation Fluxtion must have access to the classpath that includes user classes and Fluxtion libraries. The compiler loads all classes for analysis and instantiates nodes for adding to the graph. 

Some of the classes that Fluxtion libraries provide are only required at compile time as they are used to construct the graph for analysis, but once the SEP is generated these classes are not required. A set of the Fluxtion classes are required at both generation time and application runtime. Fluxtion splits its classes into libraries with the following naming convention:

| library name | build time | runtime | description |
| :--- | :--- | :--- | :--- |
| api | yes | yes | provides runtime classes such as events, or lifecycle interfaces |
| builder | yes | - | classes used by client code in graph construction such as SEPConfig |

A user may choose to remove the builder classes from the deployment of an application and there should be no effect on the generated SEP.

## CLI

Executing the help command with Fluxtion will print to console the usage reference:

```text
Problem parsing command line, Unrecognized option: --help
usage: fluxtion-generate [-bc <arg>] [-bd <directory>] [-cc <class name>]
       -cp <classpath> [-d] [-df <arg>] [-dt <velocity file>] [-fc <class
       name>] [-fs <arg>] [-gb <arg>] [-gd <arg>] [-nc <class name>] -oc
       <class name> [-od <directory>] -op <package name> [-or <directory>]
       [-pa <arg>] [-rf <class name>] [-st <velocity file>] [-yc <yml
       file>]
 -bc,--buildClasses <arg>                 Compile classes from generated
                                          source.
 -bd,--buildDirectory <directory>         The directory where the SEP will
                                          compile
 -cc,--configClass <class name>           The SEPConfig class fluxtion
                                          will use to construct the SEP.
 -cp,--classPath <classpath>              Classpath fluxtion uses to find
                                          application classes referenced
                                          in the SEPBuilder class.
                                          Separator char ';'.
 -d,--debug                               debug log output
 -df,--supportDirtyFiltering <arg>        If set at least one parent must
                                          return true from an event
                                          handling method for an event to
                                          propogate to dependent nodes.
 -dt,--sepDebugTemplate <velocity file>   Override the debug template file
                                          fluxtion will use to generate
                                          the debug SEP.
 -fc,--filterNamingClass <class name>     Override default naming strategy
                                          for native filters names in
                                          generated SEP.
 -fs,--formatSource <arg>                 Format generated source.
 -gb,--generateDebugPrep <arg>            Generate a debug version oi the
                                          SEP for using with fluxtion
                                          debug tool.
 -gd,--generateDescription <arg>          Generate a meta-data description
                                          of SEP.
 -nc,--nodeNamingClass <class name>       Override default naming strategy
                                          for variable names in generated
                                          SEP.
 -oc,--outClass <class name>              Simple class name for the
                                          generated SEP. This class is the
                                          entry point for event
                                          processing.
 -od,--outDirectory <directory>           The directory where the SEP will
                                          generate source artifacts.
 -op,--outPackage <package name>          Package name for the generated
                                          SEP.
 -or,--outResDirectory <directory>        The directory where the SEP will
                                          generate non runtime artifacts,
                                          such as debug information.
 -pa,--assignPrivate <arg>                If set generated SEP will
                                          attempt to use reflection style
                                          assignment for private
                                          non-transient members
 -rf,--rootFactoryClass <class name>      The root factory SEP will use
                                          when using factory generation.
 -st,--sepTemplate <velocity file>        Override the template file
                                          fluxtion will use to generate
                                          the SEP.
 -yc,--yamlFactoryConfig <yml file>       Yaml file for multiple factory
                                          configurations in a single SEP.

```

### Example use

Most of the parameters are self explanatory and can configure the compilation to deliver a specific set of goals. A standard usage of Fluxtion to generate a SEP from a supplied SEPConfig file similar to that below:

```bash
java -jar fluxtion.jar -outDirectory d:\example\updated-reference-core/src/main/java -buildDirectory d:\example\updated-reference-core/target/classes -outResDirectory d:\example\updated-reference-core/src/main/resources -outPackage com.fluxtion.example.core.building.injection.generated -configClass com.fluxtion.example.core.building.injection.Builder -outClass SampleProcessor -buildClasses true -formatSource true -supportDirtyFiltering true -generateDebugPrep false -generateDescription true -assignPrivate false -cp d:\example\updated-reference-core\target\classes;C:\Users\dhv\.m2\repository\com\fluxtion\fluxtion-api\1.5.4-SNAPSHOT\fluxtion-api-1.5.4-SNAPSHOT.jar;C:\Users\dhv\.m2\repository\it\unimi\dsi\fastutil\7.0.7\fastutil-7.0.7.jar;C:\Users\dhv\.m2\repository\net\vidageek\mirror\1.6.1\mirror-1.6.1.jar;C:\Users\dhv\.m2\repository\com\fluxtion\fluxtion-builder\1.5.4-SNAPSHOT\fluxtion-builder-1.5.4-SNAPSHOT.jar

12:46:54.197 [main] INFO  org.reflections.Reflections - Reflections took 281 ms to scan 6 urls, producing 605 keys and 2965 values
12:46:54.291 [main] INFO  c.f.g.m.TopologicallySortedDependecyGraph - missing default construtor - attempting construction bypass
12:46:55.081 [main] INFO  c.f.generator.exporter.PngGenerator - png image generated:d:\example\updated-reference-core\src\main\resources\com\fluxtion\example\core\building\injection\generated\SampleProcessor.png
12:46:55.378 [main] INFO  net.openhft.compiler.CachedCompiler - Updated com.fluxtion.example.core.building.injection.generated.SampleProcessor in d:\example\updated-reference-core\target\classes
```

 

