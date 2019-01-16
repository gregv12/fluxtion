---
description: Description of build and run time environments
---

# Building, classpath and execution

## Introduction

Fluxtion is a code generator that increases developer efficiency, reduces bugs and simplifies maintenance. Our primary focus is processing a stream of application events resulting in execution of application logic. In this section we cover the plumbing aspects of Fluxtion in a Java environment:

* Build time vs run time
* Classpaths
* executing a solution

## Build time 

Fluxtion carries out an analysis on the application classes using meta-data the developer provides and generates a SEP solution. This process occurs at compile time, our metaprogramming approach shifts some of the work to compile time ahead of execution of the [SEP ](developer/static-event-processor/)solution. The Fluxtion compiler is analogous to the Java compiler and the JVM, without byte code the JVM cannot execute a program. 

The compiler loads all classes during analysis, uses meta-data to instantiate nodes that are added to the graph. This buildtime graph is analysed and used to generate the SEP solution for runtime execution.

## Run time

The generated SEP executes at run time processing the stream of application events. The generator and components used during build time are not required by the SEP. A SEP component can have a long term life time, persisting between machine restarts.

Separating Fluxtion dependencies into run and build time allows a SEP to run on environments that do not support the requirements of the build time. For example the build time may require java 11 but the SEP requires a minimum version of java 7. This is useful where production environments are fixed or a SEP is deployed on a device that does not support dynamic compilation and class loading.

## Classpath

To generate a solution Fluxtion must have access to a classpath that includes user classes, Fluxtion libraries and meta-data. 

Some of the classes that Fluxtion libraries provide are only required at compile time as they are used to construct the graph for analysis, but once the SEP is generated these classes are no longer required. A set of the Fluxtion classes are required at both generation time and application runtime. Fluxtion splits its classes into libraries with the following naming convention:

| library  | buildtime | runtime | description |
| :--- | :--- | :--- | :--- |
| [api](https://github.com/v12technology/fluxtion/tree/master/api) | yes | yes | provides runtime classes such as events, or lifecycle interfaces |
| [builder](https://github.com/v12technology/fluxtion/tree/master/builder) | yes | - | classes used by client code in graph construction such as SEPConfig |
| [generator](https://github.com/v12technology/fluxtion/tree/master/generator) | yes | - | The implementation of an event stream compiler, builder classes use the generator |

A user may choose to remove the builder classes from the deployment of an application and there should be no effect on the generated SEP. The api classes do not depend upon builder or generator. 

## Execution

Exectuing the SEP at runtime is the same as executing any java class, the required elements must be on the classpath. The only Fluxtion library required at runtime is the api library. 

