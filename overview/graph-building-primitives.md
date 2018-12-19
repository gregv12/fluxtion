---
description: Explanation of the core design time components for graph bhilding
---

# Graph building primitives

## Introduction

Previously we have seen how to configure even processing in the generated SEP. This section will concentrate on the available methods to construct the graph model that the Fluxtion event stream compiler will analyse. 

## Graph construction methods

### Annotations

Annotations provide a significant avenue for providing meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

| Annotation | Comment |
| :--- | :--- |
| `@Config` | A key value pair for configuring an injected instance. |
| `@ConfigVariable` | Marks a field as providing configuration for an injected instance |
| `@Inject` | Marks a reference to be created by a [NodeFactory](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java) and injected by Fluxtion ESC |

### Imperative

### Data driven

### Declarative

###  Yaml description



