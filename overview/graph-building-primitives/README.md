---
description: Explanation of the core design time components for graph bhilding
---

# Graph building primitives

## Introduction

Previously we have seen how to configure even processing in the generated SEP. This section will concentrate on the available methods to construct the graph model that the Fluxtion event stream compiler will analyse. 

## Graph construction methods

### Imperative

Use of a [builder class](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/SEPConfig.java) and imperative code to define the nodes that make up the execution graph.

### Data driven

Combining data with custom written [node factories ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java)to automatically add nodes to the execution graph.

### Annotations

Annotations provide a significant avenue for providing meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

| Annotation | Comment |
| :--- | :--- |
| [@Config](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/Config.java) | A key value pair for configuring an injected instance. |
| [@ConfigVariable](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/ConfigVariable.java) | Marks a field as providing configuration for an injected instance |
| [@Inject](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/Inject.java) | Marks a reference to be created by a [NodeFactory](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java) and injected by Fluxtion ESC |

Injection annotations combine with factories to inject manage nodes into an instance.

### Declarative

Use of higher order functions to declare the intention of event processing. 

###  Yaml description

A [general purpose language](https://github.com/v12technology/fluxtion/blob/master/generator/src/main/java/com/fluxtion/creator/Creator.java) for specifying nodes, events, functions and dependencies between nodes.

