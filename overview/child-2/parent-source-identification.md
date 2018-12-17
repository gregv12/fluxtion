---
description: Identify which execution path is active
---

# Parent source identification

Nodes can be on multiple execution paths and may want to know which is the active execution path during event processing. The OnEvent method does not discriminate which parent has updated, only that all parents have processed the event if they are on the execution path. A node may have conditional logic that is dependent upon the source of the parent change. Using OnEvent only would require all parents to maintain an update flag for querying by child nodes. This approach is error prone and fragile, it is better to build event source notification into the framework in a reliable manner. 

