---
description: propagate events for dirty nodes only
---

# Dirty node monitoring

The goal is to only let the execution path proceed if a node marks itself as being dirty.

Sometimes it is useful to only let the event wave progress if the current node marks itself as being dirty. For example  a level monitor will swallow the event wave unless the level is breached. An OnEvent method with a boolean return type is monitored for dirty status by Fluxtion with the following rules:

* A true value marks the node as dirty and the event wave propagates.
* A negative value swallows the event wave and no child OnEvent methods are invoked.
* No declared return from OnEvent is presumed as a persistent value of true, always propagate events .
* If a child has multiple parents an or rule is applied, so any parent change propagates the event wave

{% hint style="info" %}
Dirty filtering support can be toggled on or off using the supportDirtyFiltering property in the SEPConfig builder.
{% endhint %}



