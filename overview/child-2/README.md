---
description: Instructions used by the developer to configure event processing
---

# Event processing primitives

This section covers the primitives the developer can use in their code to guide the Fluxtion event stream compiler in creating the event dispatch logic within a SEP. In general the primitives take the form of annotations added to methods in the user created nodes. 

Each page will cover a single concept and possibly an accompanying annotation, where appropriate diagrams and pseudo code are employed to help illustrate the concept.

This section does not cover [graph construction](../graph-building-primitives.md), invoking [Fluxtion generator](../../tools/fluxtion-tool.md) or [auditing](../auditing.md) the SEP at runtime.



