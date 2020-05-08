---
description: Use of @EventHandler annotation to push data into a SEP
---

# Event handling

## Single event handler per class

The goal is to invoke a method on a user created node when an [Event](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/event/Event.java) is posted to the encapsulating SEP. 

The method must accept a single argument that is of type [Event ](https://github.com/v12technology/fluxtion/blob/develop/api/src/main/java/com/fluxtion/runtime/event/Event.java)and be marked with [@EventHandler](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/EventHandler.java) annotation. There are no requirements on method naming, only the method signature and annotation are analysed by Fluxtion. 

Fluxtion employs pattern type matching analysis to ensure the correct method dispatch occurs.

## Example

The example is located [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events/singlehandler). A single event type MyEvent will be dispateched to an event handler method, MyEventProcessor.handleEvent

### Node class

In this example  [MyEventProcessor ](https://github.com/v12technology/fluxtion/blob/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events/singlehandler/MyEventProcessor.java)handles[ MyEvent.  ](https://github.com/v12technology/fluxtion/blob/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/shared/MyEvent.java)The method is marked with the annotation [`@EventHandler`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/EventHandler.java) to identify it as an event handler method to the Fluxtion compiler at generation time.

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleEvent(MyEvent event){}
}
```

### Generated SEP

Fluxtion ESC will generate the following SEP. The interface method onEvent discovers the runtime type \(line 11\) and dipatches the event to a type specific handleEvent method \(line 20\). The execution path is invoked in the handleEvent method.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventProcessor myEventProcessor_1 = new MyEventProcessor();

  public SampleProcessor() {}

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.getClass().getName()) {
      case ("com.fluxtion.internal.fluxtionscratch.MyEvent"):
        {
          MyEvent typedEvent = (MyEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(MyEvent typedEvent) {
    //Default, no filter methods
    myEventProcessor_1.handleEvent(typedEvent);
    //event stack unwind callbacks
    afterEvent();
  }
  //content removed
}
```

### Graph builder

The SEPConfig class that constructs the graph imperatively is shown below. Use of SEPConfig is described in [imperative graph building](../graph-building-primitives/imperative.md). 

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        MyEventProcessor  handler = new MyEventProcessor();
        addNode(handler);
    }

}
```

