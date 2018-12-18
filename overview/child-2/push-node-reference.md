---
description: Enable a node to invert the execution path and push data to a parent
---

# Push node reference

The goal is to invert the execution path order of two nodes. This allows a child node to push data to a parent node before the parent node OnEvent method is invoked.

Sometimes we want to push data to a shared node for reading by another node. The problem can occur if the read node is not dependent upon the write node, as reader and writer are siblings their order in the execution path is not guaranteed. We could force the reader to artificially create a dependency upon the writer. Again we want to move this error prone operation into the framework if possible. If the writer specifies the parent node as a push reference then no reader will need to worry about execution order. The push creates an implicit dependency between reader and writer without the reader specifying anything and the processing will behave as expected.

We use the [@PushReference](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/PushReference.java) to mark a parent node as an inverted execution dependency.

#### Example

In this example we create a cache as a shared node, with a cache reader and cache writer. The reader and writer are siblings with no explicit dependency. We use the @PushReference annotation in the writer to mark the cache as a push destination.

