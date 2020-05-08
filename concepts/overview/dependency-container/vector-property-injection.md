---
description: Injecting scalar properties into a managed node
---

# Vector property injection

## Introduction <a id="introduction"></a>

Fluxtion generator will inject vector properties into the SEP at runtime. The properties are read during generation and the SEP will have the vector values hard-coded in the generated file. Using property injection client code can inject either default values or constant values into the SEP.

### Property type support <a id="property-type-support"></a>

The supported types that can be injected into a SEP member are:

* Managed node references
* byte
* char
* short
* int
* float
* long
* double
* String
* Enum

### Collection support

The following collections are supported for injection:

* Arrays
* java.util.List 

### Injection mechanism

The following access patterns for property injection are supported:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Access Pattern</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Bean getter/setter</td>
      <td style="text-align:left">Fluxtion will look for a read/write bean pattern and generate a setXXX
        call in the SEP with a locally generated array or List</td>
    </tr>
    <tr>
      <td style="text-align:left">Final fields</td>
      <td style="text-align:left">A constructor must be available that matches exactly the final fields.
        If one exists Fluxtion will generate the constructor call, with a locally
        generated array or List.</td>
    </tr>
    <tr>
      <td style="text-align:left">public fields</td>
      <td style="text-align:left">
        <p>Public fields are values are written in the generated SEP. The field must</p>
        <p>non-transient, public scope and mutable. Fluxtion will assign a locally
          generated array or List</p>
      </td>
    </tr>
  </tbody>
</table>## Example

The example demonstrates contstructor, public fields and bean pattern injection for arrays and Lists. The code generated depends upon the context of the injection.

The code for the example is located [here](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/dependencyinjection/propertyvector).

### Node class

```java
public class PropertyHandler {
    //final properties
    private final boolean[] booleanFinalProp;
    private final List<Integer> intFinalProp;
    private final String[] stringFinalProp;
    
    //public properties
    public List<Boolean> booleanPublicProp;
    public int[] intPublicProp;
    public List<String> stringPublicProp;
    public List<SampleEnum> enumPublioProp;
    
    //bean properties
    private boolean[] booleanBeanProp;
    private List<Integer> intBeanProp;
    private List<String> stringBeanProp;
    private SampleEnum[] enumBeanProp;
    
    public PropertyHandler(boolean[] booleanFinalProp, List<Integer> intFinalProp, String[] stringFinalProp) {
        this.booleanFinalProp = booleanFinalProp;
        this.intFinalProp = intFinalProp;
        this.stringFinalProp = stringFinalProp;
    }

    public boolean[] getBooleanBeanProp() {
        return booleanBeanProp;
    }

    public void setBooleanBeanProp(boolean[] booleanBeanProp) {
        this.booleanBeanProp = booleanBeanProp;
    }

    public List<Integer> getIntBeanProp() {
        return intBeanProp;
    }

    public void setIntBeanProp(List<Integer> intBeanProp) {
        this.intBeanProp = intBeanProp;
    }

    public List<String> getStringBeanProp() {
        return stringBeanProp;
    }

    public void setStringBeanProp(List<String> stringBeanProp) {
        this.stringBeanProp = stringBeanProp;
    }

    public SampleEnum[] getEnumBeanProp() {
        return enumBeanProp;
    }

    public void setEnumBeanProp(SampleEnum[] enumBeanProp) {
        this.enumBeanProp = enumBeanProp;
    }

    @EventHandler
    public void myEvent(MyEvent event){
        
    }
    
    @Initialise
    public void init(){
        //calculate and set any derived properties here
    }
}

```

### SEPConfig builder

Multiple vector access patterns are exercised in the builder. All the vector values are read at generation time and written into the generated SEP.

```java
public class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        PropertyHandler handler = addNode(new PropertyHandler(
                new boolean[]{true, true, false},
                Arrays.asList(1, 2, 3, 4, 5),
                new String[]{"one", "two"}
        ));

        List<Integer> ints = Arrays.asList(1, 2, 3, 4, 5);
        handler.setIntBeanProp(ints);
        handler.setBooleanBeanProp(new boolean[]{false, false, false, false});
        handler.setStringBeanProp(Arrays.asList("AA", "BB", "CC"));
        handler.setEnumBeanProp(new SampleEnum[]{TUESDAY, THURSDAY, SATURDAY});

        handler.booleanPublicProp = Arrays.asList(true, true);
        handler.intPublicProp = new int[]{100, 200, 300, 400};
        handler.stringPublicProp = Arrays.asList("1", "2", "3", "4");
        handler.enumPublioProp = Arrays.asList(SUNDAY, MONDAY);
    }

}
```

### Generated SEP

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final PropertyHandler propertyHandler_1 =
      new PropertyHandler(
          new boolean[] {true, true, false},
          Arrays.asList(1, 2, 3, 4, 5),
          new String[] {"one", "two"});
  //Dirty flags

  //Filter constants

  public SampleProcessor() {
    propertyHandler_1.setBooleanBeanProp(new boolean[] {false, false, false, false});
    propertyHandler_1.setEnumBeanProp(
        new SampleEnum[] {SampleEnum.TUESDAY, SampleEnum.THURSDAY, SampleEnum.SATURDAY});
    propertyHandler_1.setIntBeanProp(Arrays.asList(1, 2, 3, 4, 5));
    propertyHandler_1.setStringBeanProp(Arrays.asList("AA", "BB", "CC"));
    propertyHandler_1.intPublicProp = new int[4];
    propertyHandler_1.intPublicProp[0] = 100;
    propertyHandler_1.intPublicProp[1] = 200;
    propertyHandler_1.intPublicProp[2] = 300;
    propertyHandler_1.intPublicProp[3] = 400;
    propertyHandler_1.stringPublicProp.add("1");
    propertyHandler_1.stringPublicProp.add("2");
    propertyHandler_1.stringPublicProp.add("3");
    propertyHandler_1.stringPublicProp.add("4");
    propertyHandler_1.enumPublioProp.add(SampleEnum.SUNDAY);
    propertyHandler_1.enumPublioProp.add(SampleEnum.MONDAY);
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
    propertyHandler_1.myEvent(typedEvent);
    //event stack unwind callbacks
    afterEvent();
  }

  @Override
  public void afterEvent() {}

  @Override
  public void init() {
    propertyHandler_1.init();
  }

  @Override
  public void tearDown() {}

  @Override
  public void batchPause() {}

  @Override
  public void batchEnd() {}
}

```



