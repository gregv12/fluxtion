---
description: >-
  Complete quick start example demonstrating integration of Fluxtion into an
  application
---

# Quick start

This quick-start example demonstrates implementing a simple Unix wc like utility with Fluxtion. The user creates a set of application classes that perform the actual calculations, the application classes will be orchestrated by the generated Static Event Processor\(SEP\). Each byte will be read from a file and sent to the [SEP ](https://github.com/v12technology/fluxtion-quickstart/blob/master/src/main/java/com/fluxtion/sample/wordcount/generated/WcProcessor.java)as a [CharEvent](https://github.com/v12technology/fluxtion-quickstart/blob/master/src/main/java/com/fluxtion/sample/wordcount/CharEvent.java). At the end of the file the char, word and line counts will be printed to console.

The example will use maven as the build system, the full example is located on github [here](https://github.com/v12technology/fluxtion-quickstart). Clone the repository and then follow the example steps. 

```bash
greg@bg21:~/tmp/fluxtion-quickstart$ mvn install -P fluxtion
```

![The steps to integrate fluxtion static event processor\(SEP\) into a system using the imperative form](.gitbook/assets/fluxtion_build.png)

## Overview

{% hint style="info" %}
There is no need to code anything follow the steps against the cloned example.
{% endhint %}

#### Step 1

User writes classes representing incoming events and nodes containing business logic, annotations mark callback methods in the nodes. These classes will be used in your final application. [show me](untitled.md#step-1-1)

#### Step 2

Write a [SEPConfig](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/SEPConfig.java) that binds instances of nodes together into an object graph, this class will be used by Fluxtion generator at compile time. [show me](untitled.md#step-2-1)

#### Step 3

In your pom use the fluxtion maven plugin, specifying SEPConfig class, output package and class name. The plugin will invoke the fluxtion generator as part of the build, generating a SEP. [show me](untitled.md#step-3-1)

#### Step 4

Use the generated SEP in your code/tests by sending Event's to the `onEvent(Event e)` interface method of the SEP. [show me](untitled.md#step-4-1)

## Detailed instructions

### Step 1 - create classes

Create a standard maven pom file and add maven dependencies to Fluxtion runtime api and compile time builder, replace the \[CURRENT\_VERSION} on lines 4 and 9 with the latest release of Fluxtion. The cloned project has the complete [pom.xml](https://github.com/v12technology/fluxtion-quickstart/blob/master/pom.xml).

```markup
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-api</artifactId>
            <version>[CURRENT_VERSION]</version>
        </dependency>
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-builder</artifactId>
            <version>[CURRENT_VERSION]</version>
        </dependency>
```

Write event and business classes that the generated SEP will manage at runtime. The classes the user creates are CharEvent.java and WordCounter.java, you can see them in [github](https://github.com/v12technology/fluxtion-quickstart/tree/master/src/main/java/com/fluxtion/sample/wordcount).

[**CharEvent:**](https://github.com/v12technology/fluxtion-quickstart/blob/master/src/main/java/com/fluxtion/sample/wordcount/CharEvent.java) Extends [Event](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/event/Event.java), the content of the CharEvent is the char value. An event is the entry point to a processing cycle in the SEP.

```java
public class CharEvent extends Event{
    
    public static final int ID = 1;
    
    public CharEvent(char id) {
        super(ID, id);
        filterId = id;
    }

    public char getCharacter() {
        return (char) filterId;
    }

    /**
     * Setting the character will also make the filterId update as well
     * @param character 
     */
    public void setCharacter(char character) {
        filterId = character;
    }

    @Override
    public String toString() {
        return "CharEvent{" + getCharacter() + '}';
    }
           
}
```

The optional filter value of the event is set to the value of the char. This is the event the application will create and feed into the generated SEP.

[**WordCounter:**](https://github.com/v12technology/fluxtion-quickstart/blob/master/src/main/java/com/fluxtion/sample/wordcount/WordCounter.java) receives CharEvents and maintains a set of stateful calculations for sums of chars, words and lines. An instance of this class is created and referenced within the generated SEP, the SEP will handle all initialisation, lifecycle and event dispatch for managed nodes.

```java
public class WordCounter {

    public transient int wordCount;
    public transient int charCount;
    public transient int lineCount;
    private int increment = 1;

    @EventHandler
    public void onAnyChar(CharEvent event) {
        charCount++;
    }

    @EventHandler(filterId = '\t')
    public void onTabDelimiter(CharEvent event) {
        increment = 1;
    }

    @EventHandler(filterId = ' ')
    public void onSpaceDelimiter(CharEvent event) {
        increment = 1;
    }

    @EventHandler(filterId = '\n')
    public void onEol(CharEvent event) {
        lineCount++;
        increment = 1;
    }

    @EventHandler(filterId = '\r')
    public void onCarriageReturn(CharEvent event) {
        //do nothing handle \r\n
    }

    @EventHandler(FilterType.unmatched)
    public void onUnmatchedChar(CharEvent event) {
        wordCount += increment;
        increment = 0;
    }
    ....
}
```

The `@EventHandler` annotation attached to a single argument method, marks the method as an entry point for processing.

Some of the methods are marked with a filter value `@EventHandler(filterId = '\t')` signifying the methods are only invoked when the Event and the filter value of the event match.

### Step 2- build graph

The Builder class extends the base class [SEPConfig ](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/SEPConfig.java)and provides meta-data to the Fluxtion generator.

```java
public static class Builder extends SEPConfig {

    @Override
    public void buildConfig() {
        addPublicNode(new WordCounter(), "result");
        maxFiltersInline = 15;
    }
}
```

In this case we are adding a single node with public scoped variable "result" with `addPublicNode(new WordCounter(), "result")`. A public variable will allow us to "pull" data from the SEP using the reference. This file is used by Fluxtion at build time to generate the SEP.

### Step 3 - mvn build SEP

Now the main application classes are coded we need to generate Static Event Processor that will act as the stream processor. A maven plugin configuration in the [pom.xml](https://github.com/v12technology/fluxtion-quickstart/blob/master/pom.xml) invokes Fluxtion compiler with the correct parameters in the configuration section to drive the SEP generation.

```markup
<build>
    <plugins>
        <plugin>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-maven-plugin</artifactId>
            <version>${fluxtion.maven-plugin.ver}</version>
            <executions>
                <execution>
                    <id>wc-processor-gen</id>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                    <configuration>
                        <configClass>com.fluxtion.sample.wordcount.WordCounter$Builder</configClass>
                        <packageName>com.fluxtion.sample.wordcount.generated</packageName>
                        <className>WcProcessor</className>
                        <supportDirtyFiltering>false</supportDirtyFiltering>
                        <outputDirectory>src/main/java</outputDirectory>
                        <generateDescription>false</generateDescription>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

Explanation of the configuration parameters:

* **configClass**: The SEPConfig class Fluxtion compiler uses as source of meta-data at build time.
* **packageName**: The output package for the generated SEP.
* **className**: The simple class name for the generated SEP.
* **supportDirtyFiltering**: controls guard support for conditional processing of sub nodes.
* **outputDirectory**: Output directory for generated source used as compilation inputs.
* **generateDescription**: controls generation of SEP descriptors, eg png. Single node SEP's have none, turn off.

Running the Fluxtion maven plugin as part of the build will invoke the Fluxtion compiler. In our example the plugin is run as part of the fluxtion maven profile, so to invoke the Fluxtion use:

```bash
mvn install -P fluxtion
```

The output of the build will be SEP source file,[ WcProcessor.java](https://github.com/v12technology/fluxtion-quickstart/blob/master/src/main/java/com/fluxtion/sample/wordcount/generated/WcProcessor.java) ready for integration into your application. The generated SEP file:

```java
public class WcProcessor implements EventHandler, BatchHandler, Lifecycle {

  //Node declarations
  public final WordCounter result = new WordCounter();
  //Dirty flags

  //Filter constants

  public WcProcessor() {}

  @Override
  public void onEvent(com.fluxtion.runtime.event.Event event) {
    switch (event.eventId()) {
      case (CharEvent.ID):
        {
          CharEvent typedEvent = (CharEvent) event;
          handleEvent(typedEvent);
          break;
        }
    }
  }

  public void handleEvent(CharEvent typedEvent) {
    switch (typedEvent.filterId()) {
        //Event Class:[com.fluxtion.sample.wordcount.CharEvent] filterId:[9]
      case (9):
        result.onTabDelimiter(typedEvent);
        result.onAnyChar(typedEvent);
        afterEvent();
        return;
        //Event Class:[com.fluxtion.sample.wordcount.CharEvent] filterId:[10]
      case (10):
        result.onEol(typedEvent);
        result.onAnyChar(typedEvent);
        afterEvent();
        return;
        //Event Class:[com.fluxtion.sample.wordcount.CharEvent] filterId:[13]
      case (13):
        result.onCarriageReturn(typedEvent);
        result.onAnyChar(typedEvent);
        afterEvent();
        return;
        //Event Class:[com.fluxtion.sample.wordcount.CharEvent] filterId:[32]
      case (32):
        result.onSpaceDelimiter(typedEvent);
        result.onAnyChar(typedEvent);
        afterEvent();
        return;
    }
    //Default, no filter methods
    result.onAnyChar(typedEvent);
    result.onUnmatchedChar(typedEvent);
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

Note the WordProcessor is generated as a public final variable "result" in the SEP.

There are two levels of filtering occuring in the solutions:

* By type: only if the type matches `CharEvent` will the SEP dispatch to specific `handleEvent` method
* By value: if the value of the char matches one of the preset filters then that leg is executed, or a default un-matched branch if no match is found.

### Step 4 - integrate SEP

The generated SEP is the same as using any java source file in your development process, just code as normal. The SEP implements the well know interface [EventHandler](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/lifecycle/EventHandler.java). The application instantiates the SEP \(WcProcessor\) and sends events for processing by invoking `EventHandler.onEvent(Event e)` with a new event.

```java
public class Main {

    public static final int SIZE = 4 * 1024;

    public static void main(String[] args) {
        File f = new File(args[0]);
        try {
            streamFromFile(f);
        } catch (IOException ex) {
            System.out.println("error processing file:" + ex.getMessage());
        }
    }

    public static WcProcessor streamFromFile(File file) throws FileNotFoundException, IOException {
        long now = System.nanoTime();
        WcProcessor processor = new WcProcessor();
        processor.init();
        if (file.exists() && file.isFile()) {
            FileChannel fileChannel = new RandomAccessFile(file, "r").getChannel();
            long size = file.length();
            MappedByteBuffer buffer = fileChannel.map(
                    FileChannel.MapMode.READ_ONLY, 0, size);
            CharEvent charEvent = new CharEvent(' ');

            final byte[] barray = new byte[SIZE];
            int nGet;
            while (buffer.hasRemaining()) {
                nGet = Math.min(buffer.remaining(), SIZE);
                buffer.get(barray, 0, nGet);
                for (int i = 0; i < nGet; i++) {
                    charEvent.setCharacter((char) barray[i]);
                    processor.handleEvent(charEvent);
                }
            }
            processor.tearDown();
            double delta = ((int)(System.nanoTime() - now)/1_000_000)/1_000.0;
            System.out.println(processor.result.toString());
            System.out.printf("time: %.3f sec %n", delta);
        } else {
            System.out.println("cannot process file file:" + file.getAbsolutePath());
        }
        return processor;
    }
}
```

Most of the code handles streaming data from a file and wrapping each byte as a CharEvent. The key integration points between app and generated SEP are shown below.

The creation and intialisation of the SEP \(WcProcessor\)

```java
        WcProcessor processor = new WcProcessor();
        processor.init();
```

Pushing data to the SEP for each byte in the file

```java
        charEvent.setCharacter((char) barray[i]);
        processor.handleEvent(charEvent);
```

Pulling results from the SEP. Pull functionality is available as we declared the WcProcessors as a public node in the builder.

```java
        processor.tearDown();
        ...
        System.out.println(processor.result.toString());
```

Execute the jar that holds the application classes from the root of the quickstart example. 

```bash
c:\tmp\fluxtion-quickstart>java -jar dist\wc.jar dist\sample\norvig.txt
 48,698,162 chars
  7,439,040 words
  1,549,801 lines

time: 0.098 sec
```



