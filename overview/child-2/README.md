---
description: Instructions used by the developer to configure event processing
---

# Event processing primitives

## Introduction

This section covers the primitives the developer can use in their code to guide the Fluxtion event stream compiler in creating the event dispatch logic within a SEP. In general the primitives take the form of annotations added to methods in the user created nodes. 

Each page will cover a single concept and possibly an accompanying annotation, where appropriate diagrams and pseudo code are employed to help illustrate the concept. An accompanying set of examples are located [here](https://github.com/v12technology/fluxtion/tree/master/examples/documentation-examples), the examples for events are in the package: `com.fluxtion.example.core.events`

This section does not cover [graph construction](../graph-building-primitives/), invoking [Fluxtion generator](../../tools/fluxtion-tool.md) or [auditing](../auditing.md) the SEP at runtime.

## Event dispatch rules

The primitives are used by the ESC to generate a rule based predictable dispatcher. The dispatch rules the ESC obeys are:

| Rule  | Description |
| :--- | :--- |
| Event handler first | The entry point for an execution path is an event handler method. |
| Event data passing | The event handler method will receive the event as an argument |
| Node | A node is a no-arg instance method invoked by the event wave. |
| Execution path members | Both event handlers and nodes are on the execution path |
| Topological dispatch | Execution path members are executed in topological order |
| Member sorting | The object reference determines the sorting priority. The source  of the reference is a lower priority than the target.  |
| Sibling ordering | The execution path order of siblings is unspecified |
| Multiplexing instances | A single class can hold multiple nodes. An instance can appear  multiple times on an execution path |
| Active execution path | Only one execution path is actively dispatching.  |

## Annotations

Annotations provide a significant amount of meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

| Annotation | Comment |
| :--- | :--- |
| `@AfterEvent` | Marks a method to be called in a class when all event processing has completed.  |
| `@EventHandler` | Marks a method as an event handler, an entry point to an execution path. |
| `@FilterId` | Marks a field as the default filter for all event handlers in this class |
| `@Initialise` | An initialisation method that is invoked by the SEP before event processing. |
| `@NoEventReference` | Marks a parent node as a reference only, the child node will be excluded from the execution path of NoEventReference node. |
| `@OnBatchEnd` | Marks a method to be invoked by the SEP when a batch ends. |
| `@OnBatchPause` | Marks a method to be invoked by the SEP when a batch is paused and more messages are expected. |
| `@OnEvent` | Marks a method to be included in the execution graph. Will be called when all of its dependent nodes on the execution path have processed the event. |
| `@OnEventComplete` | The inverse of OnEvent behaviour, similar to AfterEvent but only if the node is on the execution path. |
| `@OnParentUpdate` | Marks a method to receive the Identity of the parent node\(s\) that have updated. |
| `@PushReference` | Inverts the execution path order so the child is before the parent in the event notification wave |
| `@TearDown` | A finalisation method that is invoked by the SEP after all event processing. |



