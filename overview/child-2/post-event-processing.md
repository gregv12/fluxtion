---
description: Post processing for a node after an event process cycle
---

# Post event processing

## Introduction

The goal is to provide nodes a method of resetting state after each event cycle. 

Nodes are effectively memory caches that store intermediate and permanent results of event processing. Child nodes can refer to cached values of parent nodes to perform calculations. Some of the cached values may need resetting at the end of an event cycle, and others are stored permanently. Fluxtion provides two annotations that mark a method for post event cycle processing.

| Annotation | Behaviour |
| :--- | :--- |
| @AfterEvent | Marks a method to be called when  event processing has completed. This is for any event regardless if the containing instance is on the execution path. These methods will always be executed after an event cycle. |
| @OnEventComplete | Marks a method to be called when event processing has completed. Only for node instances that are on the active execution path will be invoked.  |

