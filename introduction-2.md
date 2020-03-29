---
description: A description of Fluxtion's place in the stream processing environment
---

# Introduction 2

If you are interested in writing real-time or streaming applications we hope to persuade you to investigate Fluxtion further.

Fluxtion is a  routing utility for use within a stream processing application, automatically connecting event streams to processing pipelines. The application is statically analysed by Fluxtion to create an embedded router. With this approach complex real-time applications can be built more quickly, with less bugs and the end result is optimized for performance to reduce running costs. 

### The what, how and when for stream processing

When building an event streaming processing a developer has to answer three questions:

| Question | Answer |
| :--- | :--- |
| **WHAT**  requires processing? | Event streams are the input source to the application  |
| **HOW**  does the input need processing? | Custom written logic or re-used functions |
| **WHEN** does the processing take place? | Bespoke dispatch logic that connects events to streams |

Fluxtion is the automatic generation of the **WHEN** logic in a streaming application. For the example below Fluxtion acts as the arrows in the diagram:

![](.gitbook/assets/flow-processing-example.png)

### Why is this useful

Projects and components exist that help developers answer the what and the how questions in a streaming application. Kafka for example supplies a reliable event stream, this is **what** we process. Many utilities exist for re-use to meet application calculation requirements, this is **how** we process. 

Fluxtion is the missing piece in the puzzle. As an application develops over time the way we connect components changes and evolves requiring constant re-working. The connections become more fragile and complex leading to many bugs and time consuming maintenance, Fluxtion static analysis algorithms remove this time and money drain from the development cycle, automatically determining **when** to connect components.  

### Sample code

Fluxtion is a java utility that generates complex dispatch logic. Conceptually Fluxtion operates like a hybrid of java streams, RX java and google guava event bus. Each incoming event requires a unique execution path, those paths may combine for different events and each path is reactive.  

