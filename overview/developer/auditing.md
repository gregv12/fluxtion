# Structured processing

## Introduction

A SEP formalises the processing of application events removing the adhoc integration of dispatch logic, we name this concept **structured processing.** Similar to [structured data](https://whatis.techtarget.com/definition/structured-data) the formal nature of structured processing permits a set of tools to be created that support the analysis of the processing. 

As well as tools  humans can draw inferences from meta-data outputs that are generated because there is an understanding how the SEP will implement the processing logic. For example an image of the SEP graph will allow an analyst to quickly understand the processing logic the developer has specified. 

## Processing description outputs

At the same time as the ESC generates a SEP, a set of artifacts are generated that are consistent with the SEP and can be used to understand the logical processing. The outputs are:

### GraphML

a markup description of the execution graph encoded in the SEP. The markup  details all nodes, class types, references between nodes, event handler nodes and input events. Attributes on the nodes include:

* The class of the node
* The type of node, event handler or child node
* The class of the Event
* The variable name of the node generated in the code
* The event types feeding into an event handler

### Png of static event processor

The graphml generated is parsed and an image is generated from the parsing, sample image below:

![A png image representing a word count SEP](../../.gitbook/assets/wordfrequencyprocessor.png)

* Orange ovals - Event
* Blue rectangles - Event handler
* Green rectangle - Node

## Processing integration

When the SEP is generated Fluxtion can inject integration points into the code. These injection points allow an [Auditor ](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/audit/Auditor.java)to be integrated into the processing loop post the generation process. An auditor can be used to perform a host of functions such as:

* Structures logger, for auditing event processing
* A performance monitor
* A dynamic property tracer

The capabilities of auditing are covered in the [auditing ](../auditing.md)section.

