---
description: Use of multiple @EventHandler annotations to push data into a SEP
---

# Multiple handlers per class

## Multiple handlers

The goal is to have individual methods in a single class handle separate events.

Multiple event handler methods in a single class are supported if the Events are of different types. Another [event handler method](./) is added to the node and annotated before running the generator. 

{% hint style="info" %}
Using an _EventHandler interface_ would only allow one event type per instance, annotations allow a plurality of event handler methods in a class. 
{% endhint %}

Example below, MyEventProcessor handles MyEvent and ConfigEvent

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleEvent(MyEvent event){}
    
    @EventHandler
    public void handleConfigEvent(ConfigEvent event){}
    
}
```

## Example

The example is located [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events/multihandler).

#### Generated SEP 

Fluxtion ESC will generate the SEP below. The interface method `onEvent` implements a pattern based switch using the class name as the pattern. `onEvent` delegates to a type specific dispatcher, in the overloaded `handleEvent` methods. The overloaded methods are generated at compile time and are early bound for correct dispatch resolution  by the JVM.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventProcessor myEventProcessor_1 = new MyEventProcessor();

  public SampleProcessor() {}

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.getClass().getName()) {
      case ("com.fluxtion.internal.fluxtionscratch.ConfigEvent"):
        {
          ConfigEvent typedEvent = (ConfigEvent) event;
          handleEvent(typedEvent);
          break;
        }
      case ("com.fluxtion.internal.fluxtionscratch.MyEvent"):
        {
          MyEvent typedEvent = (MyEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(ConfigEvent typedEvent) {
    //Default, no filter methods
    myEventProcessor_1.handleConfigEvent(typedEvent);
    //event stack unwind callbacks
    afterEvent();
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

