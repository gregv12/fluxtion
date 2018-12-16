---
description: Instructions used by the developer to configure event processing
---

# Event processing primitives

This section covers the primitives the developer can use in their code to guide the Fluxtion event stream compiler in creating the event dispatch logic within a SEP. In general the primitives take the form of annotations added to methods in the user created nodes. 

Each page will cover a single concept and possibly an accompanying annotation, where appropriate diagrams and pseudo code are employed to help illustrate the concept.

This section does not cover [graph construction](../graph-building-primitives.md), invoking [Fluxtion generator](../../tools/fluxtion-tool.md) or [auditing](../auditing.md) the SEP at runtime.

Annotations provide a significant avenue for providing meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

* `@AfterEvent`:  Marks a method to be called a class when all event processing has completed. 
* `@EventHandler`: Marks a method as an event handler, and an entry point to an execution path.
* `@FilterId`: Marks a field as a default filter for all event handlers in that class.
* `@Initialise`: An initialisation method that is invoked by the SEP before event processing.
* `@NoEventReference`: Marks a parent node as a reference only, the child node will receive no event updates from the parent marked.
* `@OnBatchEnd`: Marks a method to be invoked by the SEP when a batch ends.
* `@OnBatchPause`: Marks a method to be invoked by the SEP when a batch is paused and more messages are expected.
* `@OnEvent`: Marks a method to be called when all of its dependent nodes on the execution path have processed the event.
* `@OnEventComplete`: The inverse of OnEvent behaviour, similar to AfterEvent but only if the node is on the execution path.
* `@OnParentUpdate`: Marks a method to receive the Identity of the parent node\(s\) that have updated.
* `@TearDown`: A finalisation method that is invoked by the SEP after all event processing.
* `@PushReference`: Inverts the execution path order so the child is before the parent in the event notification wave.



