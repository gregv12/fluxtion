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

### Chains of nodes

By using the NodeRegistry factories can create chains of nodes that will be added to the execution graph. 

#### Delegating factories

A single call to `NodeRegistry.findOrCreateNode` within createNode method can result in a whole graph being created, as factories recursively call findOrCreateNode in the createNode method resulting in delegated calls to new factories, until a root of an execution graph is reached.

#### Register nodes

Nodes can be created in the createNode method and registered in the graph without delegating to a factory. Use the supplied  NodeRegistry argument and invoke  registerNode, with a newly created instance.

### Configuration data

The Fluxtion tool accepts a yml file to configure factory building, the yml file lists the root factories and the initial config map to give to each factory.

Sample yml file

```yaml
rootNodeMappings: 
    com.fluxtion.learning.example20.CharacterClassifier: classifier
config:
    chars: [a, b, c, 0, 7, 8]
        
```

The rootNodeMappings is the fully qualified class name of the instance that will be the root of the execution graph. Multiple root nodes can be added as a list. The configuration map supplied to the NodeRegistry is derived from the config element in the yaml document.

## Factory discovery

Fluxtion searches the class path for factories and registers them for use by in the NodeRegistry. As long as the user created NodeRegistry is on the classpath it will be available at design time. 

## Example

This example using a yaml file to create a graph with pipeline. A FactoryNode is created by a NodeFactory. In the createNode method a pipeline is built, a FilteredHandler is instantiated and added to graph using `registry.regsiterNode()`. The node properties are set using the supplied config map.  

The example is located [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/building/factories) and the config file [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/resources/cfg).

#### FactoryNode

```java
public class FactoryNode {
    private final FilteredDataHandler handler;

    public FactoryNode(FilteredDataHandler handler) {
        this.handler = handler;
    }
    //omitted code
}
```

### Factory

```java
public class Factory extends SEPConfig implements NodeFactory<FactoryNode>  {
    
    @Override
    public FactoryNode createNode(Map config, NodeRegistry registry) {
        int limit = (Integer) config.get("limit");
        String filter = (String) config.get("filter");
        FilteredDataHandler handler = new FilteredDataHandler(filter);
        handler.setLimit(limit);
        registry.registerNode(handler, "handler");
        return new FactoryNode(handler);
    }
    
}
```

### Yaml config

```yaml
rootNodeMappings: 
    com.fluxtion.example.core.building.factories.FactoryNode: factoryBuilt
config:
    limit: 2000
    filter: myTestFilter
```

### Maven config

The maven config required to build the factory example is:

```markup
        <execution>
            <id>building-fatcory</id>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <configClass>com.fluxtion.example.core.events.collections.Builder</configClass>
                <packageName>com.fluxtion.example.core.events.collections.generated</packageName>
                <className>SampleProcessor</className>
                <outputDirectory>src/main/java</outputDirectory>
                <generateDescription>false</generateDescription>
            </configuration>
        </execution>
```

### Generated SEP

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final FilteredDataHandler handler = new FilteredDataHandler("myTestFilter");
  public final FactoryNode factoryBuilt = new FactoryNode(handler);
  //code omitted

  public SampleProcessor() {
    handler.setLimit(2000);
  }

  public void handleEvent(DataEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("myTestFilter"):
        handler.dataEvent(typedEvent);
        factoryBuilt.update();
        afterEvent();
        return;
    }
    afterEvent();
  }

}

```

### Generated png

![Sample SEP built using factories and yaml config](../../../.gitbook/assets/sampleprocessor%20%2814%29.png)

