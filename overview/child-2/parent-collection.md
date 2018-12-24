---
description: Use of collections to store references to parent nodes
---

# Parent collection

## Introduction

The goal is to store a set of parent references and interact with them meaningfully.

Sometimes we want to manage a set of parents as a collection and not as scalar values. Fluxtion supports collections and arrays of parents. Any graph node that is a member of a collection will be assigned in the generated SEP at construction time.

The @OnEvent method of the aggregating class will be invoked after all parents on the execution path have been updated.

### Parent change identification

Sometimes it is useful to know which element of a collection has updated. The same logic that Fluxtion supports for identifying scalar [parent changes](parent-source-identification.md) works for collections. Use the `@OnParentUpdate` annotation to make a callback method for notification, the method should accept a single argument that represents the element in the collection that has updated. 

Because the notification is connected to a collection, multiple invocations of the `@OnParentUpdate` may occur in a single event cycle, once for each element updated.

If multiple collections of the same type are tracked, the value on the @OnParentUpdate annotation can be used to route identifications from a collection to a specific method.

## Example

We present an example where a node has two collections of parent nodes. One collection is a list the other an array. We use the `@OnParentUpdate` method with a value to monitor both collections for updates in separate method callbacks.

The example is available [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events/collections).

### Aggregating class

The because the class types of the collections intersect we direct the handlers array to the `parentUpdated()` method using the value of the annotation to match the source collection variable name, see line 11.

```java
public class Aggregator {

    private final Object[] handlers;
    private final List<ConfigHandler> cfgHandlers;

    public Aggregator(Object[] handlers, List<ConfigHandler> cfgHandlers) {
        this.handlers = handlers;
        this.cfgHandlers = cfgHandlers;
    }

    @OnParentUpdate("handlers")
    public void parentUdated(Object parent) {}

    @OnParentUpdate
    public void parentCfgUdated(ConfigHandler parent) {}
    
    @OnEvent
    public void update(){}

}
```

### SEPConfig builder

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        MyEventHandler handler1 = addNode(new MyEventHandler());
        MyEventHandler handler2 = addNode(new MyEventHandler());
        DataEventHandler handler3 = addNode(new DataEventHandler());
        ConfigHandler cfg0 = addNode(new ConfigHandler());
        ConfigHandler cfg1 = addNode(new ConfigHandler());
        ConfigHandler cfg2 = addNode(new ConfigHandler());
        List<ConfigHandler> cfgHandlers = Arrays.asList(cfg1, cfg2);
        Aggregator agg = addNode(new Aggregator(new Object[]{handler1, handler2, handler3, cfg0}, cfgHandlers));
    }

}
```

### Generated SEP

The SEP creates and assigns the collections before any event processing. All parent update monitoring methods are invoked as expected and the Aggregator onEvent method is invoked after all parents have been visited.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final ConfigHandler configHandler_7 = new ConfigHandler();
  private final ConfigHandler configHandler_9 = new ConfigHandler();
  private final ConfigHandler configHandler_11 = new ConfigHandler();
  private final DataEventHandler dataEventHandler_5 = new DataEventHandler();
  private final MyEventHandler myEventHandler_1 = new MyEventHandler();
  private final MyEventHandler myEventHandler_3 = new MyEventHandler();
  private final Aggregator aggregator_13 =
      new Aggregator(
          new Object[] {myEventHandler_1, myEventHandler_3, dataEventHandler_5, configHandler_7},
          Arrays.asList(configHandler_9, configHandler_11));
  //code omitted for clarity

  public void handleEvent(ConfigEvent typedEvent) {
    //Default, no filter methods
    configHandler_7.cfgUpdate(typedEvent);
    aggregator_13.parentUdated(configHandler_7);
    configHandler_9.cfgUpdate(typedEvent);
    aggregator_13.parentCfgUdated(configHandler_9);
    configHandler_11.cfgUpdate(typedEvent);
    aggregator_13.parentCfgUdated(configHandler_11);
    aggregator_13.update();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(DataEvent typedEvent) {
    //Default, no filter methods
    dataEventHandler_5.handleEvent(typedEvent);
    aggregator_13.parentUdated(dataEventHandler_5);
    aggregator_13.update();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(MyEvent typedEvent) {
    //Default, no filter methods
    myEventHandler_1.handleEvent(typedEvent);
    aggregator_13.parentUdated(myEventHandler_1);
    myEventHandler_3.handleEvent(typedEvent);
    aggregator_13.parentUdated(myEventHandler_3);
    aggregator_13.update();
    //event stack unwind callbacks
    afterEvent();
  }

}

```

### Generated png

![Sample SEP with collection  of parents](../../.gitbook/assets/sampleprocessor%20%2811%29.png)



