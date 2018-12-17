# Event graph

 The goal is to create many unique execution paths where a node is invoked only if it is on the active execution path.

The [@OnEvent](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnEvent.java) annotation marks a method to be included in the execution graph as per the [event pipeline](event-pipeline.md) description. In this case we want to create a graph and not a pipeline, the execution graph will contain multiple execution paths. The uniqueness of an execution paths  is the compound value of event type and filter.

For Fluxtion there is no difference between a graph and a pipeline, the use of [processing inference](../developer/child-1/graph-building.md#processing-inference) determines the structure of the graph. Similarly for the programmer there is no change, only the creation of a rich object model is required.

The example below creates a processing graph, using the builder below:

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        DataEventHandler handler = new DataEventHandler();
        PipelineNode node = new PipelineNode(handler);
        MyEventHandler myHandler = new MyEventHandler();
        CombinerNode combiner = new CombinerNode(myHandler, handler);
        addNode(handler);
        addNode(myHandler);
        addNode(node);
        addNode(combiner);
    }

}
```

