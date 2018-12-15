---
description: Use of multiple @EventHandler annotations to push data into a SEP
---

# Multiple handlers per class

The goal is to have individual methods in a single class handle separate evens.

Multiple event handler methods in a single class are supported if the Events are of different types. Another event handler method is added to the node and annotated before re-running the generator. 

{% hint style="info" %}
Using an _EventHandler interface_ would only allow one event type per instance, annotations allow a plurality of event handler methods in a class. 
{% endhint %}

Example below, MyEventProcessor handles MyEvent and ConfigEvent

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleEvent(MyEvent event){
        
    }
    
    @EventHandler
    public void handleConfigEvent(ConfigEvent event){
        
    }
}
```

 Fluxtion ESC will generate the following SEP:

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventProcessor myEventProcessor_1 = new MyEventProcessor();
  //Dirty flags

  //Filter constants

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

