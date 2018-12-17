---
description: Identify which execution path is active
---

# Parent source identification

A Node on multiple execution paths may want to know which is the active execution path in order to condition the processing it performs. The OnEvent method does not discriminate which parent has updated, only that all parents have processed the event if they are on the execution path. Using OnEvent on its own would require all parents to maintain a status flag that a child could query. This approach is error prone, fragile and adds work to all node developers. A better solution is reliably build event source notification into the framework. 

Fluxtion provides parent change identification by using the  [`@OnParentUpdate`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnParentUpdate.java) annotation. 

{% hint style="info" %}
Track parent changes to remove parent checking logic from the OnEvent method
{% endhint %}

The following rules apply when using [`@OnParentUpdate`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnParentUpdate.java) annotation:

* Mark a single argument method to track a parent for change. 
* The argument is the type of the parent to track.
* The method will be invoked if the parent is on the active execution path.
* The method will be invoked after the parent's OnEvent method has executed.
* The method will be invoked before the OnEvent method in this class.
* Multiple tracking methods can exist in one class.
* For multiple parents of the same type, set the value of the annotation to the field name of the parent.
* For array fields use a scalar value, this will be the element in the array that has updated.
* For array fields the marked method may be invoked multiple times in a cycle.







