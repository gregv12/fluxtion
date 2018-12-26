---
description: SEP managing lifecycle and injecting properties to managed nodes
---

# Dependency container

## Introduction

The generated SEP acts as dependency injection container for managed beans, this section discusses those capabilities. The SEP container provides the following features:

* Managed node references
  * Instantiating managed nodes in topological order.
  * Setting scalar references between managed nodes.
  * Setting vector references between managed nodes.
* Property Setting
  * Setting scalar properties on nodes.
  * Setting vector properties on nodes.
  * Reflection based property setting.
* Invoking lifecycle methods on managed nodes.

The managed node references is covered extensively in the [event processing](../child-2/) and [graph building](../graph-building-primitives/) sections. This section deals with property setting and lifecycle methods.

