---
description: injection of audit into event processing
---

# Auditing

## Introduction

The goal is to inject an external auditor into the event processing cycle so all event and node invocations can be monitored by the auditor. 

When the SEP is generated there is an option to add an [Auditor](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/audit/Auditor.java). The auditor will be invoked by the SEP automatically as the execution path progresses.

## Audit capabilities

The user develops or re-uses a class that implements the Auditor interface and registers this via the SEPConfig method `addAuditor(name, Auditor)`. The auditor indicates whether it wants to track individual node invocations or just event tracking by responding true/false in the `auditInvocations` method:

* true - receive all lifecycle callbacks from the auditor interface
* false - receive all lifecycle callbacks from the auditor interface

   except `nodeInvoked`

The Auditor can monitor the following SEP invocations:

* nodes registered - type and node name
* event received - event instance
* individual node invocations -  node instance, nodeName, methodName of node, event instance
* post processing complete

In the Auditor callbacks the name of the node in code and the method invoked are supplied as strings. This is consistent with the names used in the generated SEP, graphml and png.

## Example

The example below demonstrates an auditor that tracks all node processing.

### Auditor

```java
public class NodeAuditor implements Auditor{

    @Override
    public void nodeRegistered(Object node, String nodeName) {}

    @Override
    public void eventReceived(Object event) {}

    @Override
    public void processingComplete() {}

    @Override
    public void tearDown() {}

    @Override
    public void nodeInvoked(Object node, String nodeName, String methodName, Object event) {}

    @Override
    public boolean auditInvocations() {
        return true;
    }
    
}
```

### SEPConfig

The auditor is imperatively added to the graph using the addAuditor method on line 10.

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        addNode(new Combiner( 
                addNode(new ChildNode(addNode(new MyEventHandler()))),
                addNode(new PipelineNode(addNode(new DataEventHandler())))
        ));
        
        addAuditor(new NodeAuditor(), "nodeAuditor");
    }
}
```

### Generated SEP

The generated SEP shows each stage of event processing from node registration to individual node invocations are tracked by the auditor

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final DataEventHandler dataEventHandler_5 = new DataEventHandler();
  private final MyEventHandler myEventHandler_1 = new MyEventHandler();
  private final ChildNode childNode_3 = new ChildNode(myEventHandler_1);
  private final PipelineNode pipelineNode_7 = new PipelineNode(dataEventHandler_5);
  private final Combiner combiner_9 = new Combiner(childNode_3, pipelineNode_7);
  public final NodeAuditor nodeAuditor = new NodeAuditor();
  //Dirty flags
  private boolean isDirty_combiner_9 = false;
  //Filter constants

  public SampleProcessor() {
    //node auditors
    initialiseAuditor(nodeAuditor);
  }

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.getClass().getName()) {
      case ("com.fluxtion.example.shared.ConfigEvent"):
        {
          ConfigEvent typedEvent = (ConfigEvent) event;
          handleEvent(typedEvent);
          break;
        }
      case ("com.fluxtion.example.shared.DataEvent"):
        {
          DataEvent typedEvent = (DataEvent) event;
          handleEvent(typedEvent);
          break;
        }
      case ("com.fluxtion.example.shared.MyEvent"):
        {
          MyEvent typedEvent = (MyEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(ConfigEvent typedEvent) {
    auditEvent(typedEvent);
    //Default, no filter methods
    auditInvocation(combiner_9, "combiner_9", "processConfig", typedEvent);
    isDirty_combiner_9 = combiner_9.processConfig(typedEvent);
    auditInvocation(combiner_9, "combiner_9", "onEvent", typedEvent);
    combiner_9.onEvent();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(DataEvent typedEvent) {
    auditEvent(typedEvent);
    //Default, no filter methods
    auditInvocation(dataEventHandler_5, "dataEventHandler_5", "handleEvent", typedEvent);
    dataEventHandler_5.handleEvent(typedEvent);
    auditInvocation(pipelineNode_7, "pipelineNode_7", "update", typedEvent);
    pipelineNode_7.update();
    auditInvocation(combiner_9, "combiner_9", "onEvent", typedEvent);
    combiner_9.onEvent();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(MyEvent typedEvent) {
    auditEvent(typedEvent);
    //Default, no filter methods
    auditInvocation(myEventHandler_1, "myEventHandler_1", "handleEvent", typedEvent);
    myEventHandler_1.handleEvent(typedEvent);
    auditInvocation(childNode_3, "childNode_3", "recalculate", typedEvent);
    childNode_3.recalculate();
    auditInvocation(combiner_9, "combiner_9", "onEvent", typedEvent);
    combiner_9.onEvent();
    //event stack unwind callbacks
    afterEvent();
  }

  private void auditEvent(Object typedEvent) {
    nodeAuditor.eventReceived(typedEvent);
  }

  private void auditInvocation(Object node, String nodeName, String methodName, Object typedEvent) {
    nodeAuditor.nodeInvoked(node, nodeName, methodName, typedEvent);
  }

  private void initialiseAuditor(Auditor auditor) {
    auditor.init();
    auditor.nodeRegistered(combiner_9, "combiner_9");
    auditor.nodeRegistered(childNode_3, "childNode_3");
    auditor.nodeRegistered(dataEventHandler_5, "dataEventHandler_5");
    auditor.nodeRegistered(myEventHandler_1, "myEventHandler_1");
    auditor.nodeRegistered(pipelineNode_7, "pipelineNode_7");
  }

  @Override
  public void afterEvent() {
    nodeAuditor.processingComplete();
    isDirty_combiner_9 = false;
  }

  @Override
  public void init() {}

  @Override
  public void tearDown() {
    nodeAuditor.tearDown();
  }

  @Override
  public void batchPause() {}

  @Override
  public void batchEnd() {}
}

```

### Generated png

![](../.gitbook/assets/sampleprocessor%20%2810%29.png)

