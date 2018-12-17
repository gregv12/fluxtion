---
description: Identify which execution path is active
---

# Parent source identification

A Node on multiple execution paths may want to know which is the active execution to condition the processing it performs. The OnEvent method does not discriminate which parent has updated, only that all parents have processed the event if they are on the execution path. Using OnEvent only would require all parents to maintain an update flag for querying by child nodes. This approach is error prone and fragile, it is better to build event source notification into the framework in a reliable manner. 

Fluxtion provides parent change identification by using the  [`@OnParentUpdate`](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/OnParentUpdate.java) annotation. The following rules apply when using this annotation:

* Mark a single argument method, the argument is the type of the parent to track.
* The callback will be invoked before the OnEvent method in this class.
* For multiple parents of the same type, set the value of the annotation to the field name.
* For array fields use a scalar value, this will be the element in the array that has updated.
* For array fields the marked method may be invoked multiple times in a cycle.





