---
description: Extension give Fluxtion additional functionality
---

# Introduction

Fluxtion plugins are available that extend the functionality of the base framework. Each plugin will usually have an api and a builder jar, following the same classpath and naming convention described [here](../overview/building-classpath-and-execution.md#classpath). The extensions that are available for use are:

| Extension |  | description |
| :--- | :--- | :--- |
| declarative |  | Provides declarative functionality to define graph nodes. Removes the need  to imperatively declare a graph. Some higher order functions provided |
|  | group | Aggregate data with grouping. Provides derived aggregate calculations. |
|  | log | Log to consoled with zero gc, triggered by update notifications |
|  | numeric | Numeric functions declaratively described. |
|  | predicate | Declarative functional predicates operating on nodes |
|  | filter | FIlters nodes using predicates. |
|  | window | WIndow strategies and functions operating on numeric values. |
|  |  |  |
| fu-text |  | Functional operators for using with ascii text. Builds on the declarative framework supplied by declarative extension. All the builder functions use the declarative form. |
|  | csv | Parse, transform, validate, event dispatch for CSV file inputs. |
|  | ascii | Ascii pattern matches, extractors and primitive conversions. |
|  | event | Event forwarding between SEP's |
|  | math | Basic set of math functions |
|  | filter | text filtering |





