# Event processing

### Graph processing primer

In a stream processor events are received and processed with predictable results. A set of dependent behaviours can be modelled as a **directed acyclic graph**. Each behaviour is a node or vertex on the graph, in our case these behaviours are functions encapsulated in object instances. The references between instances that encapsulate nodes are the edges of the graph. 

In Fluxtion terms we call this instance graph an **execution graph**. There are many paths through any graph, however within a SEP there are special paths called **execution paths**, it is only these execution paths that the SEP makes available for event processing. An execution path has the following characteristics:

* The first node on an execution path is an event handler.
* An execution path is formed of nodes that have the first event handler as a parent.
* Functions are nodes on the execution path.
* An event handler is a node that accepts an incoming event for processing.
* Functions will always be invoked in execution path order.
* Execution path order is topologically sorted such that all parent nodes are invoked before child nodes.
* A child node will be given a valid reference to each parent dependency before any event processing occurs.

In an execution graph the number of execution paths is dependent upon the number of event handlers in the graph. 

{% hint style="info" %}
Events are dispatched in **topological order** on the execution path
{% endhint %}

### Topological order

We define a predictable order on the execution path as the [topological order](https://en.wikipedia.org/wiki/Topological_sorting). Topological ordering is an intuitive concept, but we provide an example for illustration. The diagram below shows an execution graph with multiple execution paths.

![Example execution graph with multiple execution paths](../../../.gitbook/assets/execution_graph_paths.png)

For the diagram above we can categorise the elements as follows:

* **Event graph:** Node 1, Node 2, Node 3, Node 4, Node 10, Node 11
* **Event handlers:** Node 1, Node 10
* **Execution Paths:**
  * Event A: Node 1, Node 2, Node 3 Node 4, Node 11
  * Event B: Node 10, Node 11

### Events

The entry point into a SEP is the `onEvent(Event e)` method defined in [EventHandler](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/lifecycle/EventHandler.java), which accepts any instance that extends the abstract class [Event](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/event/Event.java). An application either defines or re-uses events that extend Event. 

An event provides an entry point to inject new state into the SEP and being a processing loop.

{% hint style="info" %}
Posting an event to the SEP, is **pushing data** into the execution graph
{% endhint %}

### Nodes

Nodes in the execution graph are invoked if they lie on the active execution path. A node is any function in a java class marked with a specific Fluxtion annotation. The node class does not have to implement an interface or extend a base class, a so-called pojo \(plain old java object\) is sufficient. 

For a node to be in the SEP the containing object instances must be marked for inclusion. Methodologies for including instances in the graph are described in[ graph building](../child-1/graph-building.md) section.

### Marking event methods

There are several annotations Fluxtion defines to mark methods as being in the execution graph, two key annotations are:

* `@eventHandler` – marks a method as a candidate event handler.
* `@onEvent` – marks a method as a candidate node in an execution graph.

Event annotations and implied capabilities are covered in detail in the[ event processing primitives](../../child-2/) section. 

### Event waves and data

As described previously a SEP invokes an ordered list of event notifications along the active execution path, beginning with the event handler node. Fluxtion does not send data from node to node. The event handler receives the new event as an argument, dependent nodes are notified with a no argument method a change has occurred in one of the nodes dependencies. This is analogous to throwing a rock in a pond, it is the ripples that travel along the water and not the rock, the rock is the event and the ripple the event notification.

### 

