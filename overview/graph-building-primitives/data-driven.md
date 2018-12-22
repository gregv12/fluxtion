---
description: Use configuration data to drive graph building
---

# Data driven

## Introduction

The goal is to register nodes and event handlers in an execution graph using configuration data.

The imperative approach to adding nodes to an execution graph is not always the best fit, sometimes it is more appropriate to use data to configure the building of an execution graph. Fluxtion supports this approach using the [NodeFactory ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java)and [NodeRegistry ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeRegistry.java)interfaces:

## Node factory and node registry

[NodeFactory ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java): A user implemented class that is responsible for creating objects and registering nodes in the NodeRegistry. As part of the generation process Fluxtion invokes the callback into client factories to create a node:

[NodeRegistry](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeRegistry.java):  Holds all the currently registered nodes in a graph. If a node cannot be  found then a NodeFactory will be used to create a new instance, using the provided map for configuration.

The map of config data can be used to configure the created instance, Fluxtion automatically adds the created node to the execution graph.

NodeFactory is a generic class, Fluxtion inspects the generic type and registers the factory as creating the class of the generic type. Once the factory is registered the NodeRegistry creates instances by delegating to a NodeFactory to create instances of the type. Fluxtion will check the equals and hashcode of any node generated with the instances currently in the execution graph, if there is a match Fluxtion will substitute the newly created instance with a reference to the existing instance in the graph. 

By using the NodeRegistry factories can create chains of nodes that will be added to the execution graph. A single call to `NodeRegistry.findOrCreateNode` within createNode method can result in a whole graph being created, as factories recursively call findOrCreateNode in the createNode method resulting in delegated calls to new factories, until a root of an execution graph is reached.

Fluxtion can either search the class path for factories or factories can be explicitly registered with the tool. The Fluxtion tool accepts a yml file to configure factory building, the yml file lists the root factories and the initial config map to give to each factory.

Sample yml file

```yaml
rootNodeMappings: 
    com.fluxtion.learning.example20.CharacterClassifier: classifier
config:
    chars: [a, b, c, 0, 7, 8]
        
```

