---
description: Injecting scalar properties into a managed node
---

# Scalar property injection

## Introduction

Fluxtion generator will inject properties into the SEP at runtime. The properties are read at generation time and the generated SEP will have the values hard-coded in the generated file. Using property injection client code can inject either default values or constant values into the SEP. 

### Property type support

The supported types that can be injected into a SEP member are:

* byte
* char
* short
* int
* float
* long
* double
* String
* Enum

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
        <br
        />call in the SEP</td>
    </tr>
    <tr>
      <td style="text-align:left">Final fields</td>
      <td style="text-align:left">A constructor must be available that matches exactly the final fields.
        <br
        />If one exists Flxution will generate the constructor call.</td>
    </tr>
    <tr>
      <td style="text-align:left">public fields</td>
      <td style="text-align:left">
        <p>Public fields are values are written in the generated SEP. The field must</p>
        <p>non-transient, public scope and mutable.</p>
      </td>
    </tr>
  </tbody>
</table>Constructor/field matching will use the logic described [here](../graph-building-primitives/imperative.md#construction-rules).

## Example

The [example ](https://github.com/v12technology/fluxtion/tree/develop/examples/documentation-examples/src/main/java/com/fluxtion/example/core/dependencyinjection/propertyscalar)demonstrates all the capabilities of scalar property injection: final fields with constructors, public properties, bean properties and transient properties. The generated SEP contains the values set in the builder.

Node class

```java
public class PropertyHandler {
    //final properties
    private final boolean booleanFinalProp;
//ommitted properties
    private final SampleEnum enumFinalProp;
    //public properties
    public boolean booleanPublicProp;
//ommitted properties
    public SampleEnum enumPublicProp;
    //bean properties
    private boolean booleanBeanProp;
//ommitted properties
    private SampleEnum enumBeanProp;
    //transient properties - ignored
    public transient boolean booleanTransientProp;
//ommitted properties
    public transient SampleEnum enumTransientProp;

    public PropertyHandler(boolean booleanFinalProp, byte byteFinalProp, char charFinalProp, short shortFinalProp, float floatFinalProp, int intFinalProp, double doubleFinalProp, long longFinalProp, String stringFinalProp, SampleEnum enumFinalProp) {
        this.booleanFinalProp = booleanFinalProp;
        this.byteFinalProp = byteFinalProp;
        this.charFinalProp = charFinalProp;
        this.shortFinalProp = shortFinalProp;
        this.floatFinalProp = floatFinalProp;
        this.intFinalProp = intFinalProp;
        this.doubleFinalProp = doubleFinalProp;
        this.longFinalProp = longFinalProp;
        this.stringFinalProp = stringFinalProp;
        this.enumFinalProp = enumFinalProp;
    }

    public boolean isBooleanBeanProp() {
        return booleanBeanProp;
    }

    public void setBooleanBeanProp(boolean booleanBeanProp) {
        this.booleanBeanProp = booleanBeanProp;
    }
    
    public byte getByteBeanProp() {
        return byteBeanProp;
    }

    public void setByteBeanProp(byte byteBeanProp) {
        this.byteBeanProp = byteBeanProp;
    }

    public char getCharBeanProp() {
        return charBeanProp;
    }

    public void setCharBeanProp(char charBeanProp) {
        this.charBeanProp = charBeanProp;
    }

    public short getShortBeanProp() {
        return shortBeanProp;
    }

    public void setShortBeanProp(short shortBeanProp) {
        this.shortBeanProp = shortBeanProp;
    }

    public float getFloatBeanProp() {
        return floatBeanProp;
    }

    public void setFloatBeanProp(float floatBeanProp) {
        this.floatBeanProp = floatBeanProp;
    }

    public int getIntBeanProp() {
        return intBeanProp;
    }

    public void setIntBeanProp(int intBeanProp) {
        this.intBeanProp = intBeanProp;
    }

    public double getDoubleBeanProp() {
        return doubleBeanProp;
    }

    public void setDoubleBeanProp(double doubleBeanProp) {
        this.doubleBeanProp = doubleBeanProp;
    }

    public long getLongBeanProp() {
        return longBeanProp;
    }

    public void setLongBeanProp(long longBeanProp) {
        this.longBeanProp = longBeanProp;
    }

    public String getStringBeanProp() {
        return stringBeanProp;
    }

    public void setStringBeanProp(String stringBeanProp) {
        this.stringBeanProp = stringBeanProp;
    }

    public SampleEnum getEnumBeanProp() {
        return enumBeanProp;
    }

    public void setEnumBeanProp(SampleEnum enumBeanProp) {
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

The builder sets the property values for reading at generation time.

```java
public class Builder extends SEPConfig{

    @Override
    public void buildConfig() {
        PropertyHandler handler = addNode(new PropertyHandler(true, (byte)0,'a',(short)0,0.0f,0,0.0d,0L,"0", MONDAY));
        //boolean
        handler.booleanPublicProp = true;
        handler.booleanTransientProp = true;
        handler.setBooleanBeanProp(true);
        //bytes
        handler.bytePublicProp = (byte)1;
        handler.byteTransientProp = (byte)2;
        handler.setByteBeanProp((byte)3);
        //char
        handler.charPublicProp = 'b';
        handler.charTransientProp = 'c';
        handler.setCharBeanProp('d');
        //short
        handler.shortPublicProp = (short)1;
        handler.shortTransientProp = (short)2;
        handler.setShortBeanProp((short)3);
        //float
        handler.floatPublicProp = (float)1.1;
        handler.floatTransientProp = (float)2.2;
        handler.setFloatBeanProp((float)3.3);
        //int
        handler.intPublicProp = (int)1;
        handler.intTransientProp = (int)2;
        handler.setIntBeanProp((int)3);
        //double
        handler.doublePublicProp = (double)1.1;
        handler.doubleTransientProp = (double)2.2;
        handler.setDoubleBeanProp((double)3.3);
        //long
        handler.longPublicProp = (long)1l;
        handler.longTransientProp = (long)2l;
        handler.setLongBeanProp((long)3l);
        //enum
        handler.enumPublicProp = TUESDAY;
        handler.enumTransientProp = WEDNESDAY;
        handler.setEnumBeanProp(THURSDAY);

    }
    
}

```

### Generated SEP

The generated SEP contains the member values as read at generation time. Complex constructors, bean pattern and public fields are all supported. 

#### Note

* Transient field values are not written in the SEP.
* All values set before constructor exits

```java
public class SampleProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  private final PropertyHandler propertyHandler_1 =
      new PropertyHandler(true, (byte) 0, 'a', (short) 0, 0.0f, 0, 0.0, 0L, "0", SampleEnum.MONDAY);
  //Dirty flags

  //Filter constants

  public SampleProcessor() {
    propertyHandler_1.setBooleanBeanProp(true);
    propertyHandler_1.setByteBeanProp((byte) 3);
    propertyHandler_1.setCharBeanProp('d');
    propertyHandler_1.setDoubleBeanProp(3.3);
    propertyHandler_1.setEnumBeanProp(SampleEnum.THURSDAY);
    propertyHandler_1.setFloatBeanProp(3.3f);
    propertyHandler_1.setIntBeanProp(3);
    propertyHandler_1.setLongBeanProp(3L);
    propertyHandler_1.setShortBeanProp((short) 3);
    propertyHandler_1.booleanPublicProp = (boolean) true;
    propertyHandler_1.bytePublicProp = (byte) 1;
    propertyHandler_1.charPublicProp = 'b';
    propertyHandler_1.shortPublicProp = (short) 1;
    propertyHandler_1.floatPublicProp = (float) 1.1;
    propertyHandler_1.intPublicProp = (int) 1;
    propertyHandler_1.doublePublicProp = (double) 1.1;
    propertyHandler_1.longPublicProp = (long) 1;
    propertyHandler_1.enumPublicProp = com.fluxtion.example.shared.SampleEnum.TUESDAY;
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

