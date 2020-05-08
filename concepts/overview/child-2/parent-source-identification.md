---
description: Identify which execution path is active
---

# Parent source identification

## Introduction

The goal is to identify which parent or parents of a node are on the active execution path.

A Node on multiple execution paths may want to know which is the active execution path. The OnEvent method does not discriminate which parent has updated, only that all parents have processed the event if they are on the execution path. Using OnEvent on its own would require all parents to maintain a status flag that a child could query. This approach is error prone, fragile and adds work to all node developers. A better solution is to reliably build event source notification into the framework. 

Fluxtion provides parent change identification by using the  [`@OnParentUpdate`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnParentUpdate.java) annotation. 

{% hint style="info" %}
Track parent changes using **@OnParentUpdate** to remove parent checking logic from the @OnEvent method
{% endhint %}

The following rules apply when using [`@OnParentUpdate`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnParentUpdate.java) annotation:

* Mark a single argument method to track a parent for change. 
* The argument is the type of the parent to track.
* The method will be invoked if the parent is on the active execution path.
* The method will be invoked after the parent's OnEvent method has executed.
* The method will be invoked before the OnEvent method in this class.
* Multiple tracking methods can exist in one class, to track multiple parents.
* For multiple parents of the same type, set the value of the annotation to the field name of the parent.
* For array fields use a scalar value, this will be the element in the array that has updated.
* For array fields the marked method may be invoked multiple times in a cycle.

## Example

The following example demonstrates execution path identification for a child node of type ParentIdentifier. The code for the example is located [here](https://github.com/v12technology/fluxtion/tree/master/examples/documentation-examples/src/main/java/com/fluxtion/example/core/events/parent).

### Node class

This class has multiple parents and resides on multiple execution paths. We mark three methods with @OnParentUpdate. Invocation of these methods tells us explicitly which execution path we are on before the @OnEvent method is invoked.

Two of the parents are of the same type \(DataEventHandler\) by using the field name in the annotation we can route the notifications in a predictable manner.

```java
public class ParentIdentifier {
    private final DataEventHandler datahandler_1;
    private final DataEventHandler datahandler_2;
    private final MyEventHandler myHandler;

    public ParentIdentifier(DataEventHandler datahandler_1, DataEventHandler datahandler_2, MyEventHandler myHandler) {
        this.datahandler_1 = datahandler_1;
        this.datahandler_2 = datahandler_2;
        this.myHandler = myHandler;
    }
    
    @OnParentUpdate("datahandler_1")
    public void dataHandler_1_changed(DataEventHandler handler){}
    
    @OnParentUpdate("datahandler_2")
    public void dataHandler_2_changed(DataEventHandler handler){}
    
    @OnParentUpdate
    public void myEventHandler_changed(MyEventHandler handler){}
    
    @OnEvent
    public void process(){}
}
```

### SEPConfig builder

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        DataEventHandler datahandler_1 = addNode(new DataEventHandler());
        DataEventHandler datahandler_2 = addNode(new DataEventHandler());
        MyEventHandler myHandler = addNode(new MyEventHandler());
        ParentIdentifier identifier = addNode(new ParentIdentifier(datahandler_1, datahandler_2, myHandler));
    }

}
```

### Generated SEP

#### Note 

* Regardless of the execution path the @OnEVent method  `pathIdentifier_7.process()` is always called.
* For DataEvent's, `pathIdentifier_7.dataHandler_XX_changed` are called separately.
* For MyEvent, `pathIdentifier_7.myEventHandler_changed` is called.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final DataEventHandler dataEventHandler_1 = new DataEventHandler();
  private final DataEventHandler dataEventHandler_3 = new DataEventHandler();
  private final MyEventHandler myEventHandler_5 = new MyEventHandler();
  private final ParentIdentifier parentIdentifier_7 =
      new ParentIdentifier(dataEventHandler_1, dataEventHandler_3, myEventHandler_5);

    //code omitted for clarity

  public void handleEvent(DataEvent typedEvent) {
    //Default, no filter methods
    dataEventHandler_1.handleEvent(typedEvent);
    parentIdentifier_7.dataHandler_1_changed(dataEventHandler_1);
    dataEventHandler_3.handleEvent(typedEvent);
    parentIdentifier_7.dataHandler_2_changed(dataEventHandler_3);
    parentIdentifier_7.process();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(MyEvent typedEvent) {
    //Default, no filter methods
    myEventHandler_5.handleEvent(typedEvent);
    parentIdentifier_7.myEventHandler_changed(myEventHandler_5);
    parentIdentifier_7.process();
    //event stack unwind callbacks
    afterEvent();
  }
}

```

### Generated png

 The graphical representation of this execution graph as generated by Fluxtion ESC:

![SEP with parent identification](../../../.gitbook/assets/sampleprocessor%20%289%29.png)

