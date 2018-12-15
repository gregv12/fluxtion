# Graph building

## Introduction

A set of nodes is constructed into a processing graph by the event stream compiler. This section details how nodes are included by the event stream compiler\(ESC\) and what rules the ESC applies to generate the SEP. 

{% hint style="info" %}
Graph building occurs at compile time, the ESC is not required at runtime, it is only the SEP that processes application events
{% endhint %}

Some applications may choose to embed the ESC, but there is no requirement for this. To build a graph with  the ESC  need two pieces of information:

* The set of nodes are to be included in the compilation analysis.
* What dictates the order of nodes, so life-cycle methods are called in a predictable manner.

The first of these, node selection is provided by the developer. This is similar to any dependency injection container, for example Spring may use a spring.xml to select beans for management. The second is automatically derived by the ESC using a process called [processing inference](graph-building.md#processing-inference).

Keeping the SEP and ESC into separate process spaces creates the following benefits:

* Construction logic can be validated separately to application processing logic.
* Sensitive construction ip may be hidden and only the SEP is distributed.
* Conversely sensitive node ip may be separated from the constructor of the graph, as nodes may be provided as obfuscated libraries.
* Source and target languages can be separated.

## Node selection

There are a many strategies Fluxtion provides to create the set of nodes that are in the execution graph. The  user must provide some information as it is not possible to infer the set of nodes purely from undecorated classes. The detail of the node selection strategies are covered in the [event processing primitives](../../child-2.md) section, available options include:

* Imperative form in code
* Data driven with factories
* Member injection via annotations
* Yaml based declaration
* Spring xml
* Declarative form

Any and old methods can be combined to make the construction as extensible as possible.

## Processing Inference:

Using the configuration supplied Fluxtion infers the processing intent of the developer to model the execution graph and paths. 

In a traditional event processing system, the developer needs to specify nodes and vertices either explicitly or via an intermediate language. This is time consuming, difficult to learn, error prone, requires maintenance and does not scale due to the combinatorial complexity of graph descriptions. Additonally there is no guarantee the manual approach is optimised or even correct. 

Fluxtion addresses all these issues as it automates this time consuming and difficult task of producing an optimised set of execution paths. Fluxtion creates an instance graph at build time using the supplied configuration and then analyses the object references between instances. It is the object references that Fluxtion uses to establish the parent-child relationship and therefore the processing order. 

{% hint style="info" %}
For an instance to be included in the SEP the target of the reference must be marked for analysis
{% endhint %}

