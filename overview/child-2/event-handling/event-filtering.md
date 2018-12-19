---
description: Optional filtering on incoming events to target specific event handlers
---

# Event filtering

The goal is to support rules at the EventHandler to filter the event instances dispatched by the SEP to an event handler method. 

Fluxtion allows filtering of event dispatch so that only event handlers that match certain criteria will receive the dispatched event. The filtering rules that Fluxtion supports are:

* By event type 
* By event type and event value
* By event type and unmatched against event value

{% hint style="warning" %}
Event filtering is statically generated at compile time and cannot be altered at runtime
{% endhint %}

### Filtering by type

Filtering by event type is demonstrated in the [Multiple handlers per class](multiple-handlers-per-class.md) example.

### Filtering by type and value

The [EventHandler](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/EventHandler.java) annotation allows optional arguments for setting match filters. An incoming event can specify a filter value as either a String or an integer at runtime, whereas the event handler filter value is fixed at compile time. If the filter value of the incoming event matches that of the event handler then a dispatch will occur. The base class [Event](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/event/Event.java) provides member variables for filter string and filter int.

The following example demonstrates String filtering for a configuration event:

```java
public class MyEventProcessor {
    
    @EventHandler
    public void handleConfigEvent(ConfigEvent event){}
    
    @EventHandler(filterString = "timeout")
    public void handleTimeoutConfig(ConfigEvent event){}
    
    @EventHandler(filterString = "maxConnection")
    public void handleMaxConnectionsConfig(ConfigEvent event){}
}
```

#### Generated SEP

The following SEP is produced. Note that  `myEventProcessor_1.handleConfgEvent` will receive all ConfigEvent's as it has no filter applied:

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

//previous handlers omitted for clarity

    @EventHandler(FilterType.unmatched)
    public void unHandledConfig(ConfigEvent event) {}
}
```

#### Generated SEP

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

### Filtering by class literal

Strings as filter values can lead to fragile api's especially when refactoring. To combat this a class literal can be supplied as a filter value in the EventHandler, Fluxtion converts the class literal to a String and filters by String in the generated SEP, as shown in the example below:

```java
public class MyEventProcessor {

//previous handlers omitted for clarity
    
    @EventHandler(filterStringFromClass = Date.class)
    public void dateConfig(ConfigEvent event) {}
}
```

#### Generated SEP

Notice the class literal used in line 23, producing the following generated SEP:

```java
  public void handleEvent(ConfigEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("java.util.Date"):
        myEventProcessor_1.dateConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
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
```

### Filter variable

The value of the filter in the eventhandler method can be provided from an instance variable in the eventhandler node. This instance variable is read once at compile time for the generation phase. A string match between the 

```java
public class MyEventProcessor {

    private final String configFilter;

    public MyEventProcessor(String configFilter) {
        this.configFilter = configFilter;
    }
    
//previous handlers omitted for clarity
    
    @EventHandler(filterVariable = "configFilter")
    public void handleMyVariableConfig(ConfigEvent event) {}
}
```

#### Generated SEP

Produces the resulting SEP if the instance variable configFilter is set to "cfg.acl":

```java
  public void handleEvent(ConfigEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("cfg.acl"):
        myEventProcessor_1.handleMyVariableConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
      case ("java.util.Date"):
        myEventProcessor_1.dateConfig(typedEvent);
        myEventProcessor_1.handleConfigEvent(typedEvent);
        afterEvent();
        return;
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
```

