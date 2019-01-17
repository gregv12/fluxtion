---
description: Text processing capabilities supported by Fluxtion
---

# Fu-text

## Introduction

Fluxtion supports the processing of text streams. The streams may be received from an external publisher like a news outlet or from a local document like a csv file. Each stream is deconstructed into a set of [CharEvent ](https://github.com/v12technology/fluxtion/blob/master/extensions/fu-text/api/src/main/java/com/fluxtion/ext/futext/api/event/CharEvent.java)and [EofEvent ](https://github.com/v12technology/fluxtion/blob/master/extensions/fu-text/api/src/main/java/com/fluxtion/ext/futext/api/event/EofEvent.java) events for processing within a SEP.

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

The Fluxtion generator is not required on the classpath for build time, but the tool used at build time must have access to the Fluxtion generator. An example would be the [maven plugin ](../../tools/maven-plugin.md)that encapsulates the Fluxtion generator.



