---
description: Statically control broadcast and subscription of event notifications
---

# Event propagation control

## Introduction

The goal is to statically control the publishing and subscription of event notifications. 

Event propagation is not always desirable in an execution graph. Certain nodes may decide to opt out of the execution path. There are two dimensions we want to control, the sending of event notifications from an EventHandler and the subscription of node to the updates of a parent node.

We can control dynamically the event broadcast using a dirty flag from the OnEvent method, but this falls short of the requirements we want to meet:

* Statically defined behaviour that cannot be toggled.
* An EventHandler can decide to halt broadcasts to all child nodes.
* A child node can filter all notification updates from a particular parent node.

### Publisher control

A node may have multiple event handler methods, and some should not initiate an execution path cycle. For example a config event does not initiate the event cycle but an application event will do. This is the event publisher controlling the broadcast.

### Subscriber control

Similarly a child node may refer to a cache node but updates to the cache node will be ignored. The cache will be accessed when another parent node broadcasts a change notification, whereas another client may want to process all cache updates. This is the client controlling its own subscription.

## Annotation support

Fluxtion provides two annotations that control the notification publication.

| Annotation | Behaviour |
| :--- | :--- |
| [@NoEventReference](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/annotations/NoEventReference.java) | Removes this class as a subscriber to the marked nodes event subscriber list. |
| [@EventHandler \(propagate= false\)](https://github.com/v12technology/fluxtion/blob/master/api/src/main/java/com/fluxtion/runtime/lifecycle/EventHandler.java) | Propagate enables publication of events to child nodes. |



