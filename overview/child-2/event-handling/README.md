---
description: Use of @EventHandler annotation to push data into a SEP
---

# Event handling

## Single handler per class

Mark a method to receive an [Event](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/event/Event.java) when posted to the encapsulating SEP. The method must accept a single argument that is of type Event and be marked with [@EventHandler](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/EventHandler.java) annotation. There are no requirements on method naming, only the method signature and annotation are analysed by Fluxtion. 

Example below, MyEventProcessor handles MyEvent:

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

