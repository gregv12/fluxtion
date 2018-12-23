---
description: Use injection annotations to create a graph
---

# Injection

## Introduction

The goal is to combine imperative and factories instance creation to produce a SEP. A SEPConfig class is written by the user, but in contrast to [imperative declaration](imperative.md) only one node needs to be added, all other nodes will be added to the graph using injection.

## Injecting

Use the `@inject` annotation to inject a node instance into another node. The injected instance will be automatically included in the execution graph. If a NodeFactory cannot be located for the target type of the injection then the default constructor will be used to instantiate the node. If a default constructor cannot be found then a constructor bypass strategy is employed. An injection annotation follows the form:

```java
@Inject
public <Type> injectedInstance;
```

### Properties

It is also possible to set properties of the injected instance, the properties can be static or variable based as described below. The properties are passed to the injected factory in a map. If no factory is registered for the instance then the properties are injected directly into the member field.

For field injection primitive types and Strings are supported.

#### Static properties

This example shows setting a value of 150 in the field limit of an injected instance. There is no factory registered so the field will be set to the ca

```java
   @Config(key = "limit", value = "150")
```

#### Variable properties

The value of a config element can be looked up from a variable in the current class. An example setting the field filter in the inject class from the variable filterName in this class.

```java
@ConfigVariable(field = "filterName", key = "filter")
```

## Example

The following example injects a FilteredDataHandler into a child class \(InjectingDataProcessor\). Static and variable properties are set using the @Config and @ConfigVariable annotations. Constructor bypass is used to invoke a default constructor during graph building.

### Node classes

```java
public class FilteredDataHandler {
    
    public final String filter;
    private int limit;

    public FilteredDataHandler(String filter) {
        this.filter = filter;
    }

    @EventHandler(filterVariable = "filter")
    public void dataEvent(DataEvent event){
        
    }

    public int getLimit() {
        return limit;
    }

    public void setLimit(int limit) {
        this.limit = limit;
    }
    
}
```

```java
public class InjectingDataProcessor {
    
    private final String filterName;
    
    @Inject
    @ConfigVariable(field = "filterName", key = "filter")
    @Config(key = "limit", value = "150")
    public FilteredDataHandler filter;

    public InjectingDataProcessor(String filterName) {
        this.filterName = filterName;
    }

}
```

### SEPConfig

The SEPConfig file adds a single node to the graph, InjectingDataProcessor. All config is passed from the 

```java
public class Builder extends SEPConfig{

    @Override
    public void buildConfig() {
        addNode(new InjectingDataProcessor("myfilter_string"));
    }
    
}
```

### Generated SEP

Note the injected instance, also has properties injected, and one of the these is used to control the filter value of the event handler me, `dataEvent(DataEvent event)`

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final FilteredDataHandler filteredDataHandler_2 =
      new FilteredDataHandler("myfilter_string");
  private final InjectingDataProcessor injectingDataProcessor_1 =
      new InjectingDataProcessor("myfilter_string");
  //code omitted for clarity

  public SampleProcessor() {
    filteredDataHandler_2.setLimit(150);
    injectingDataProcessor_1.filter = filteredDataHandler_2;
  }

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.getClass().getName()) {
      case ("com.fluxtion.example.shared.DataEvent"):
        {
          DataEvent typedEvent = (DataEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(DataEvent typedEvent) {
    switch (typedEvent.filterString()) {
      case ("myfilter_string"):
        filteredDataHandler_2.dataEvent(typedEvent);
        afterEvent();
        return;
    }
    afterEvent();
  }

}

```

