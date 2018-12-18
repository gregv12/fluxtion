# Memory model

## Data transfer

On their own nodes are not enough for meaningful processing, how does each node receive new data to affect a state change? We have seen in [event processing](event-processing.md) that only the first event handler receives new data as an event, subsequent nodes on the execution path receive event notifications only. 

The concept to understand is the memory model that Fluxtion employs, each node is stateful. A child can ask its parent for data it exposes. As nodes are invoked in execution path order, all parents will have a chance to update their state before being queried by dependent nodes.

The generated SEP ensures that before any event processing can occur all object references are set between nodes as described in the execution graph. Because all references are valid, a child can ask its parent for any data it requires during execution, for example a summing node can ask its parent what the current value is. Normal rules of object encapsulation apply so a node can decide how it exposes data, and what mutability is appropriate. The key point to understand is: 

{% hint style="info" %}
In Fluxtion event notification flows as a wave,  **data is cached within nodes**.
{% endhint %}

## Side effects and stateless processing

At first it seems impossible to support no side effect processing in Fluxtion because each node is stateful. This is true if you see the no side effect boundary as the method boundary, in Fluxtion we separate the no side effect boundary from the method invocation:

{% hint style="info" %}
The no side effect boundary exists at the exit of the event handler invocation
{% endhint %}

Nodes are intermediate steps of a calculation, they can be reset when the event processing has finished for the incoming event. If a node wishes to reset itself it can annotate a method with the [`@AfterEvent`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/AfterEvent.java) annotation. This marks a method to be called when all event processing has completed, but before a new event will be accepted by the SEP. 

AfterEvent methods will be called in reverse topological order, i.e. after all dependents have finished processing their reset methods, this gives a safe methodology to remove any state changes before the next event can be processed. 

### 

