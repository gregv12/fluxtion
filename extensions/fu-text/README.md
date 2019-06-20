---
description: Text processing capabilities supported by Fluxtion
---

# Text

## Introduction

Fluxtion supports processing of text streams using the extension module [fu-text](https://github.com/v12technology/fluxtion/tree/master/extensions/fu-text). Text streams may be received from an external publisher like a news outlet or from a local document like a csv file. Each stream is deconstructed into a set of [CharEvent](https://github.com/v12technology/fluxtion/blob/master/extensions/fu-text/api/src/main/java/com/fluxtion/ext/futext/api/event/CharEvent.java) and [EofEvent](https://github.com/v12technology/fluxtion/blob/master/extensions/fu-text/api/src/main/java/com/fluxtion/ext/futext/api/event/EofEvent.java) events for processing within a SEP.

Fu-text exposes text processing as higher order functions to define logical text processing. A declarative higher-order approach is used to simplify defining text processing within a SEP.  A function creates a node and adds it to the graph for use by other nodes either declaratively or imperatively.

The example below demonstrates the power of higher order functions combined with Fluxtion. We generate a SEP that processes CSV input, maps all non-transient fields from a provided bean class and marshalls to a bean instance for well formed rows. The parser is zero gc and handles millions of records per second. An event sink is added as node to this SEP, forwarding valid bean instances as events to a child SEP.

```java
EventHandler sep  = sepInstance((c) -> 
                                csvMarshaller(WorldCityBeanTransient.class).build()
                                , pckg, className);
```



## Requirements

| fu-text lib | scope | dependency |
| :--- | :--- | :--- |
| [fluxtion-fu-text-api](https://github.com/v12technology/fluxtion/tree/master/extensions/fu-text/api) | runtime | fluxtion-declarative-api |
|  |  | fluxtion-api |
| [fluxtion-fu-text-builder](https://github.com/v12technology/fluxtion/tree/master/extensions/fu-text/builder/src/main/java/com/fluxtion/ext/futext/builder) | buildtime | fluxtion-declarative-builder |
|  |  | fluxtion-api |
|  |  | fluxtion-declarative-api |
|  |  | fluxtion-builder |
|  | Inprocess | fluxtion-declarative-builder |
|  |  | fluxtion-api |
|  |  | fluxtion-declarative-api |
|  |  | fluxtion-builder |
|  |  | fluxtion-generator - non classpath |

The Fluxtion generator is not required on the classpath at build time, but the tool used at build time must have access to the Fluxtion generator. An example would be the [maven plugin](../../tools/maven-plugin.md) that encapsulates the Fluxtion generator.



