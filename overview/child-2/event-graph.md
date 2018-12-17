# Event graph

The goal is to create many unique execution paths where a node is invoked only if it is on the active execution path.

The [@OnEvent](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnEvent.java) annotation marks a method to be included in the execution graph as per the [event pipeline](event-pipeline.md) description. In this case we want to create a graph and not a pipeline, the execution graph will contain multiple execution paths. The uniqueness of an execution paths  is the compound value of event type and filter.

For Fluxtion there is no difference between a graph and a pipeline, the use of [processing inference](../developer/child-1/graph-building.md#processing-inference) determines the structure of the graph. Similarly for the programmer there is no change in the annotations used, only the creation of a rich object model is required. Once a rich object graph is provided Fluxtion will generate a SEP with multiple execution paths.

The example below creates a processing graph, using the builder below:

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        DataEventHandler sharedHandler = addNode(new DataEventHandler());
        PipelineNode node = addNode(new PipelineNode(sharedHandler));
        ChildNode childNode = addNode(new ChildNode(node));
        MyEventHandler myHandler = addNode(new MyEventHandler());
        CombinerNode combiner = addNode(new CombinerNode(myHandler, sharedHandler));
    }

}
```

The generated SEP creates a complex dispatch method that implements multiple unique execution paths:

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final DataEventHandler dataEventHandler_1 = new DataEventHandler();
  private final MyEventHandler myEventHandler_7 = new MyEventHandler();
  private final CombinerNode combinerNode_9 =
      new CombinerNode(myEventHandler_7, dataEventHandler_1);
  private final PipelineNode pipelineNode_3 = new PipelineNode(dataEventHandler_1);
  private final ChildNode childNode_5 = new ChildNode(pipelineNode_3);

  //code omitted for clarity

  public void handleEvent(DataEvent typedEvent) {
    //Default, no filter methods
    dataEventHandler_1.handleEvent(typedEvent);
    combinerNode_9.update();
    pipelineNode_3.update();
    childNode_5.recalculate();
    //event stack unwind callbacks
    afterEvent();
  }

  public void handleEvent(MyEvent typedEvent) {
    //Default, no filter methods
    myEventHandler_7.handleEvent(typedEvent);
    combinerNode_9.update();
    //event stack unwind callbacks
    afterEvent();
  }
  
}

```

The partnering png generated Fluxtion ESC is shown below:

![a diagram show multiple execution paths in a SEP](../../.gitbook/assets/sampleprocessor%20%281%29.png)

