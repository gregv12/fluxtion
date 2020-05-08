---
description: SEP managing lifecycle and injecting properties to managed nodes
---

# Dependency injection container

## Introduction

The generated SEP acts as dependency injection container for managed nodes, this section discusses DI capabilities. A generated SEP provides the following dependency injection features:

* Managed node references
  * Instantiating managed nodes in topological order.
  * Assigning scalar references between managed nodes.
  * Assigning vector references between managed nodes.
  * Reflection based reference assignment.
  * All reference are set before SEP init method exits.
* Property Setting
  * Setting scalar properties on nodes.
  * Setting vector properties on nodes.
  * Reflection based property setting.
  * All values set before SEP constructor exits.
* Invoking lifecycle methods on managed nodes in a predictable order.

The managed node references is covered extensively in the [event processing](../../../examples/reference/child-2/) and [graph building](../../../examples/reference/graph-building-primitives/) sections. This section deals with property setting and lifecycle methods.

