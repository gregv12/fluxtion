---
description: Imperative construction of an execution graph
---

# Imperative

## Introduction

The goal is to construct a graph described imperatively in code.

Fluxtion provides a builder class, [SEPConfig ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/SEPConfig.java)that can be extended by the user to implement imperative description logic. The Fluxtion event stream compiler consults the SEPConfig sub-class to build an execution graph. Specifying the SEPConfig file to use by Fluxtion is covered in the [tool section](../../tools/fluxtion-tool.md). 

Analysis is performed with the following rules:

* Fluxtion instantiates the builder class at build time.
* Application classes are loaded at compile time for analysis.
* The `buildConfig()` method is invoked before analysis. A user can add nodes to the graph in their overriden `buildConfig()` implementation.
* Node instances are instantiated in buildConfig\(\) and added to SEPConfig data structures imperatively by the user.
* With imperative construction only nodes added to the SEPConfig data structures will be included in the final graph.
* The SEPConfig data structures are analysed and the graph built after  buildConfig\(\) has exited.

The data structures from SEPConfig that are read by Fluxtion compiler are:

| Structure | Helper method | Description |
| :--- | :--- | :--- |
| nodeList |  &lt;T&gt; T addNode\(T node\) | List of node instances in graph. Private scope |
| publicNodes | &lt;T&gt; T addPublicNode\(T node, String name\) | Map nodes with variable name. Public final scope  |
| auditorMap | &lt;T&gt; T addAuditor\(T listener, String name\) | Auditor to include with variable name. Public scope |

SEPConfig provides other members that can control the code generation process, but the data structures above are the ones used to include the instances on the final graph. Further discussion of the SEPConfig is in the [tools section](../../tools/fluxtion-tool.md).

## Construction rules

To generate the final code Fluxtion employs the following rules for reference setting in the SEP.

* References can public, setter bean pattern or constructor.
* private final members will be considered as constructor parameters.
* A constructor must exist that matches exactly the number and type of private final members.
* If there are multiple args of the same type, then the parameter name will be used to match arg to member. Use [-parameters](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javac.html) switch in javac to generate parameter names at compile time.

## Example

The following example demonstrates adding three nodes for inclusion in the generated SEP. A number  of reference setting are used:

* constructor and final fields
* bean pattern
* public variable

```java
public class Builder extends SEPConfig{

    @Override
    public void buildConfig() {
        MyEventHandler handler = addNode(new MyEventHandler());
        SubNode subNode = addPublicNode(new SubNode(handler), "subNode");
        PropertySubNode propNode = new PropertySubNode();
        propNode.setMySubNode(subNode);
        propNode.someParent = handler;
        addNode(propNode, "propNode");
    }
}
```

### **Notes**

Each node must be individually added to the graph using addNode or addPublicNode. Using addPublicNode we can create a public variable of type SubNode declared with the name "subNode" in the final SEP. The add methods return a reference to the node added.

### Generated SEP

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final MyEventHandler myEventHandler_1 = new MyEventHandler();
  public final SubNode subNode = new SubNode(myEventHandler_1);
  public final PropertySubNode propNode = new PropertySubNode();

  public SampleProcessor() {
    propNode.setMySubNode(subNode);
    propNode.someParent = myEventHandler_1;
  }
//code omitted for clarity
}
```

A number of reference setting strategies are used in the generated SEP including: constructor based, bean pattern and public variable. The constructor is used in node declarations, while the other methods are used in the constructor of the SEP.

### Generated png

![](../../.gitbook/assets/sampleprocessor%20%2811%29.png)



