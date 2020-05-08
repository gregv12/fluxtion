---
description: reflection based property injection
---

# Reflection property injection

## Introduction

Occasionally a managed node may not expose its properties or constructors making it difficult to inject managed nodes or properties. This can happen if a class uses a static factory method that invokes a private constructor. 

Fluxtion supports private member assignment and synthetic default constructor usage. All scalar and vector properties are supported in earlier sections. Non-transient fields will be ignored.

## Warning

The Jvm security model is evolving and it is unlikely reflection support and synthetic default constructors will be available in the future. This facility should be validated on your target runtime.

{% hint style="danger" %}
Check reflection works on your target platform, support is nor guaranteed by Fluxtion.
{% endhint %}

## Usage

The use of refelction assignment has to be explicitly enabled in the Fluxtion compiler with the following option:

```text
 -pa,--assignPrivate <arg>                If set generated SEP will
                                          attempt to use reflection style
                                          assignment for private
                                          non-transient members
```

With the Fluxtion maven plugin the feature must be explicitly enabled:

```markup
fluxtion:generate
  Description: A mojo to wrap the invocation of the Fluxtion executable.
  Implementation: com.fluxtion.maven.FluxtionGeneratorMojo
  Language: java
  Bound to phase: compile
​
  Available parameters:
​
    assignNonPublicMembers (Default: false)
      User property: assignNonPublicMembers
      Override whether the generated static event processor supports reflection
      based assignment for initialisation.
```

## Example

The following example demonstrates reflection based assignment for a class with a static factory method, private constructor and no mutator methods for fields.

The code is located [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/dependencyinjection/reflection).

### Node class

The factory method, build acts as a gateway to the private constructor and fields.

```java
public class FactoryNode {  
    
    private final Object parent;
    private int limit;
    private transient double transientValue;

    public FactoryNode(Object parent) {
        this.parent = parent;
    }
    
    public static final FactoryNode build(Object parent, int limit){
        FactoryNode node = new FactoryNode(parent);
        node.limit = limit;
        node.transientValue = limit * 100;
        return node;
    }

    public Object getParent() {
        return parent;
    }

    public int getLimit() {
        return limit;
    }
    
    @OnEvent
    public void onEvent(){}
    
}
```

### SEPConfig builder

```java
public class Builder extends SEPConfig{

    @Override
    public void buildConfig() {
        addNode(FactoryNode.build(addNode(new MyEventHandler()), 10000));
    }
    
}
```

### Maven plugin config

The example uses maven as the build system. The configuration to enable reflection assignment is shown below. The critical element is on line 11

```markup
<assignNonPublicMembers>true</assignNonPublicMember>
```

Full config

```markup
    <execution>
        <id>property-refelection</id>
        <goals>
            <goal>generate</goal>
        </goals>
        <configuration>
            <configClass>com.fluxtion.example.core.dependencyinjection.reflection.Builder</configClass>
            <packageName>com.fluxtion.example.core.dependencyinjection.reflection.generated</packageName>
            <className>SampleProcessor</className>
            <outputDirectory>src/main/java</outputDirectory>
            <assignNonPublicMembers>true</assignNonPublicMembers>
        </configuration>
    </execution>
```

Generated SEP

Note the use of Mirror to invoke a synthetic default constructor that does not exist and reflection to mutate the fields of the created node.

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  final net.vidageek.mirror.dsl.Mirror constructor = new net.vidageek.mirror.dsl.Mirror();
  private final MyEventHandler myEventHandler_1 = new MyEventHandler();
  private final FactoryNode factoryNode_3 =
      constructor.on(FactoryNode.class).invoke().constructor().bypasser();
  //Dirty flags

  //Filter constants

  public SampleProcessor() {
    final net.vidageek.mirror.dsl.Mirror assigner = new net.vidageek.mirror.dsl.Mirror();
    assigner.on(factoryNode_3).set().field("parent").withValue(myEventHandler_1);
    assigner.on(factoryNode_3).set().field("limit").withValue((int) 10000);
  }

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.getClass().getName()) {
      case ("com.fluxtion.example.shared.MyEvent"):
        {
          MyEvent typedEvent = (MyEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(MyEvent typedEvent) {
    //Default, no filter methods
    myEventHandler_1.handleEvent(typedEvent);
    factoryNode_3.onEvent();
    //event stack unwind callbacks
    afterEvent();
  }

  @Override
  public void afterEvent() {}

  @Override
  public void init() {}

  @Override
  public void tearDown() {}

  @Override
  public void batchPause() {}

  @Override
  public void batchEnd() {}
}
```

