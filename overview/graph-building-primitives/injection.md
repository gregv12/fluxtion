---
description: Use injection annotations to create a graph
---

# Injection

## Introduction

The goal is to combine imperative and factories instance creation to produce a SEP.

## Injecting

Use the `@inject` annotation to inject a node instance into another node. The injected instance will be automatically included in the execution graph. If a NodeFactory cannot be located for the type then the default constructor will be used to instantiate the node. If a default constructor cannot be found then a constructor bypass strategy is employed.

```java
@Inject
public <Type> injectedInstance;
```

### Static properties

It is also possible to set properties of the injected instance, the properties can be static. An example setting a value of 150 in the field limit of the injected instance

```java
   @Config(key = "limit", value = "150")
```

### Variable properties

The value of a config element can be looked up from a variable in the current class. An example setting the field filter in the inject class from the variable filterName in this class.

```java
@ConfigVariable(field = "filterName", key = "filter")
```

## Example

The following example injects a FilteredDataHandler into a child class \(InjectingDataProcessor\). Static and variable properties are set using the @Config and @ConfigVariable annotations. Constructor bypass is used to invoke a default constructor.

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

### Generated SEP

Note the injected instance, also has properties injectes, and one of the these is used to control the filter value of the event handler me, `dataEvent(DataEvent event)`

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

