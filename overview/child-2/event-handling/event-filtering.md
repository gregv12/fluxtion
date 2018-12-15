---
description: Optional filtering on incoming events to target specific event handlers
---

# Event filtering

Fluxtion allows filtering of event dispatch so that only event handlers that match certain criteria will receive the dispatched event. The filtering rules that Fluxtion supports are:

* By event type 
* By event type and event value
* By event type and unmatched against event value

### Filtering by type

Filtering by event type is demonstrated in the [Multiple handlers per class](multiple-handlers-per-class.md) example.

### Filtering by type and value

The EventHandler annotation allows optional arguments for setting match filters. An incoming event can specify a filter value as either a String or an integer. The event handler filter value is fixed at compile time. If the filter value of the incoming event matches that of the event handler then a dispatch will occur. The base class Event provides member variables for filter string and filter int.

The following example demonstrates String filtering for a configuration event:

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleConfigEvent(ConfigEvent event){    
    }
    
    @EventHandler(filterString = "timeout")
    public void handleTimeoutConfig(ConfigEvent event){
    }
    
    @EventHandler(filterString = "maxConnection")
    public void handleMaxConnectionsConfig(ConfigEvent event){
    }
}
```

The following SEP is produced. Note that `handleConfgEvent` will receive all ConfigEvent's as it has no filter applied:

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventProcessor myEventProcessor_1 = new MyEventProcessor();
 
  //omitted code for clartity
  
  public void handleEvent(ConfigEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("maxConnection"):
        myEventProcessor_1.handleMaxConnectionsConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
      case ("timeout"):
        myEventProcessor_1.handleTimeoutConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
    }
    //Default, no filter methods
    myEventProcessor_1.handleConfigEvent(typedEvent);
    //event stack unwind callbacks
    afterEvent();
  }
}
```

### Unmatched filter

Fluxtion also supports the unmatched filter case or the default branch in a case statement. Unlike a no filter that is always invoked, the unmatched filter is only invoked if there are no matches on any specified filter. The example below demonstrates a method, `unHandledConfig` that is invoked if there is an unknown config received.

```java
public class MyEventProcessor {

    @EventHandler
    public void handleEvent(MyEvent event) {
    }

    @EventHandler
    public void handleConfigEvent(ConfigEvent event) {
    }

    @EventHandler(filterString = "timeout")
    public void handleTimeoutConfig(ConfigEvent event) {
    }

    @EventHandler(filterString = "maxConnection")
    public void handleMaxConnectionsConfig(ConfigEvent event) {
    }
    
    @EventHandler(FilterType.unmatched)
    public void unHandledConfig(ConfigEvent event) {
    }
}
```

The following SEP is produced demonstrating the unmatched filter or default case:

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventProcessor myEventProcessor_1 = new MyEventProcessor();
  //code omitted for clarity

  public void handleEvent(ConfigEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("maxConnection"):
        myEventProcessor_1.handleMaxConnectionsConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
      case ("timeout"):
        myEventProcessor_1.handleTimeoutConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
    }
    //Default, no filter methods
    myEventProcessor_1.unHandledConfig(typedEvent);
    myEventProcessor_1.handleConfigEvent(typedEvent);
    //event stack unwind callbacks
    afterEvent();
  }
  
}
```

