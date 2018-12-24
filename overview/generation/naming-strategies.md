---
description: User supplied strategies for naming nodes and filters
---

# Naming strategies

## Introduction

The goal is to use a custom supplied strategy to customise node names and filters in the generated SEP. A custom naming strategy can add semantic value to member names that make supporting the generated SEP easier, than the default auto-generated names.

## Node naming

A developer can customise the node variable names declared within the final SEP by supplying Fluxtion with an instance of the [NodeNameProducer ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/generation/NodeNameProducer.java)interface. This can greatly help debugging and understanding the SEP code as contextually sensitive identifiers can be used within the SEP in place of the default cryptic auto-generated names. The interface is simple:

```java
public interface NodeNameProducer {
    String mappedNodeName(Object nodeToMap);
}
```

 The Fluxtion maven plugin accepts the fully qualified class name of a [NodeNameProducer ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/generation/NodeNameProducer.java)in its configuration section:

```markup
<nodeNamingClass>[ NodeNameProducerClass ]</nodeNamingClass>
```

## Filter naming

 A developer can customise the filter names and comments declared within the final SEP by supplying Fluxtion with an instance of the [FilterDescriptionProducer ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/generation/FilterDescription.java)interface. This can greatly help debugging and understanding the SEP code as contextually sensitive identifiers can be used within the SEP in place of the default cryptic auto-generated names. The interface is simple:

```java
public interface FilterDescriptionProducer {
     
    default FilterDescription getFilterDescription(Class<? extends Event> event, int filterId){
        FilterDescription filter = new FilterDescription(event, filterId);
        filter.comment = null;
        filter.variableName = null;
        return filter;
    }
     
    default FilterDescription getFilterDescription(Class<? extends Event> event, String filterString){
        FilterDescription filter = new FilterDescription(event, filterString);
        filter.comment = null;
        filter.variableName = null;
        return filter;
    }
     
}
```

 The Fluxtion maven plugin accepts the fully qualified class name of a [FilterDescriptionProducer ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/generation/FilterDescription.java)in its configuration section:

```markup
<filterNamingClass>[ NodeNameProducerClass ]</filterNamingClass>
```

## Example

The following example implements node and filter naming strategies. The example code is located [here](https://github.com/v12technology/fluxtion/tree/master/examples/documentation-examples/src/main/java/com/fluxtion/example/core/outstyle/naming).

Several data handlers are added to the graph in a SEPConfig. Each handler has a filter string, this value will be used to generate the node names in the SEP and the comments on the filters will be customised as well with this strategy:

```java
public class NamingStrategy implements NodeNameProducer, FilterDescriptionProducer{

    @Override
    public String mappedNodeName(Object nodeToMap) {
        if(nodeToMap instanceof DataHandler){
            return "handler_" + ((DataHandler)nodeToMap).filter;
        }
        return null;
    }

    @Override
    public FilterDescription getFilterDescription(Class<? extends Event> event, String filterString) {
        FilterDescription filterDescription = FilterDescriptionProducer.super.getFilterDescription(event, filterString); 
        if(event == DataEvent.class){
            filterDescription.comment = "filtering for " + filterString;
        }
        return filterDescription;
    }
    
}
```

### DataHandler node

```java
public class DataHandler{

    final String filter;

    public DataHandler(String filter) {
        this.filter = filter;
    }
    
    @EventHandler(filterVariable = "filter")
    public void processUpdate(DataEvent event){
        
    }
    
}
```

### SEPConfig

```java
public class Builder extends SEPConfig{

    @Override
    public void buildConfig() {
        addNode(new DataHandler("FX"));
        addNode(new DataHandler("EQUITIES"));
        addNode(new DataHandler("BONDS"));
        //prevent mapped dispatch
        maxFiltersInline = 4;
    }
    
}
```

### Generated SEP

The generated SEP now has nodes whose names are more human readable due to the naming strategy, see lines 4-6. Filter comments are customised in line with the filter strategy, lines 27, 32 and 37.

The SEP is much easier to map mentally making support a simpler task.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final DataHandler handler_FX = new DataHandler("FX");
  private final DataHandler handler_EQUITIES = new DataHandler("EQUITIES");
  private final DataHandler handler_BONDS = new DataHandler("BONDS");
  //Dirty flags

  //Filter constants

  public SampleProcessor() {}

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
        //filtering for BONDS
      case ("BONDS"):
        handler_BONDS.processUpdate(typedEvent);
        afterEvent();
        return;
        //filtering for EQUITIES
      case ("EQUITIES"):
        handler_EQUITIES.processUpdate(typedEvent);
        afterEvent();
        return;
        //filtering for FX
      case ("FX"):
        handler_FX.processUpdate(typedEvent);
        afterEvent();
        return;
    }
    afterEvent();
  }
//code omitted for clarity
}
```

