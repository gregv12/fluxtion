---
description: Use of @EventHandler annotation
---

# Event handling

## Single handler per class

Mark a method to receive an Event when posted to the encapsulating SEP. The method must accept a single argument that is of type Event and be marked with @EventHandler annotation:

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleEvent(MyEvent event){
        
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

## Multiple handlers per class

Multiple event handler methods in a single class are supported if the types are different

