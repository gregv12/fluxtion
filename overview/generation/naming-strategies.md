---
description: User supplied strategies for naming nodes and filters
---

# Naming strategies

## Introduction

the goal is to use a custom supplied strategy to customise node names and filters in the generated SEP.

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

Example



