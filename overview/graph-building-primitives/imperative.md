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

## Tool usage

The Fluxtion compiler processes the SEPConfig and the user classpath to generate the SEP.  A user invokes the executable jar [fluxtion.jar](https://github.com/v12technology/fluxtion/blob/master/generator/dist/fluxtion.jar) with a set of parameters to run a generation cycle. The compiler tool is covered in detail [here ](../../tools/fluxtion-tool.md)or used via the[ maven plugin here](../../tools/maven-plugin.md). The maven plugin is easier for the developer to integrate into the development process.

{% hint style="info" %}
The fluxtion maven plugin prints to console the command line needed to run the fluxtion.jar directly. 
{% endhint %}

The command to run the compiler takes the following form:

```text
java -jar fluxtion.jar 
-configClass [SEPConfig class]
-outClass [SEP simple class name] 
-outPackage [SEP package] 
-outDirectory [OUTPUT_DIR for SEP]
-outResDirectory [OUTPUT_DIR for resources]
-buildDirectory [CLASSES_DIR add to classpth]
-buildClasses [compile generated SEP] 
-formatSource [format SEP source] 
-supportDirtyFiltering [true/false - conditonal execution control] 
-generateDebugPrep [true/false - debugging version of SEP]  
-generateDescription [true/false - generate png/graphML]  
-assignPrivate [true/false - use reflection to assign private non transient members]  
-cp [CLASSPATH jars]
```

## Example

The following example demonstrates adding three nodes for inclusion in the generated SEP. A variety of approaches is used to set references between nodes, including:

* constructor referring to final fields - line 6
* bean pattern getter/setter - line 8
* public variable - line 9

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

### Running Fluxtion command 

For this example the command line to invoke Fluxtion ESC will be similar to the one shown below. 

```bash
java -jar fluxtion.jar -outDirectory d:\example\updated-reference-core/src/main/java -buildDirectory d:\example\updated-reference-core/target/classes -outResDirectory d:\example\updated-reference-core/src/main/resources -outPackage com.fluxtion.example.core.building.injection.generated -configClass com.fluxtion.example.core.building.injection.Builder -outClass SampleProcessor -buildClasses true -formatSource true -supportDirtyFiltering true -generateDebugPrep false -generateDescription true -assignPrivate false -cp d:\example\updated-reference-core\target\classes;C:\Users\dhv\.m2\repository\com\fluxtion\fluxtion-api\1.5.4-SNAPSHOT\fluxtion-api-1.5.4-SNAPSHOT.jar;C:\Users\dhv\.m2\repository\it\unimi\dsi\fastutil\7.0.7\fastutil-7.0.7.jar;C:\Users\dhv\.m2\repository\net\vidageek\mirror\1.6.1\mirror-1.6.1.jar;C:\Users\dhv\.m2\repository\com\fluxtion\fluxtion-builder\1.5.4-SNAPSHOT\fluxtion-builder-1.5.4-SNAPSHOT.jar

12:46:54.197 [main] INFO  org.reflections.Reflections - Reflections took 281 ms to scan 6 urls, producing 605 keys and 2965 values
12:46:54.291 [main] INFO  c.f.g.m.TopologicallySortedDependecyGraph - missing default construtor - attempting construction bypass
12:46:55.081 [main] INFO  c.f.generator.exporter.PngGenerator - png image generated:d:\example\updated-reference-core\src\main\resources\com\fluxtion\example\core\building\injection\generated\SampleProcessor.png
12:46:55.378 [main] INFO  net.openhft.compiler.CachedCompiler - Updated com.fluxtion.example.core.building.injection.generated.SampleProcessor in d:\example\updated-reference-core\target\classes
```

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



