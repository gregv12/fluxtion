---
description: A developer's guide to Fluxtion
---

# Introduction

![](.gitbook/assets/flxution-1.jpg)

## Overview

Thanks for dropping by, hope we can persuade you to donate your time to investigate Fluxtion further.

Fluxtion is a code generator that automates the production of event stream processing logic. The generated code is self-contained and designed to sit within an application. An application delivers events to the Fluxtion generated Static Event Processor for stream processing. Creating and optimising complex processing graphs is a main goal of Fluxtion.

Low latency, easy maintenance, zero gc, complex graph processing, simplified development and the "wow you can do that!!" reaction are the principles that guide our project.

As a stretch goal we would like to be the [fastest single threaded java stream processor](https://github.com/v12technology/fluxtion-quickstart/blob/master/README.md#run) on the planet.

## What are we solving

Fluxtion is focused on optimising the implementation of stream processing graphs. Other stream processors support marshalling, distributed processing, event distribution, gui's and a multitude of other features. Fluxtion presumes there is an event queue that will feed it, and concentrates solely on delivering correct and optimal execution of application logic. The burden of creating complicated calculation graphs is delegated to Fluxtion empowering developers to tackle high value projects without risk.

Want to upgrade your application logic without rewriting your infrastructure? Fluxtion is the perfect solution for you.

## Unique approach

Fluxtion is unique among stream processors as there is no need for a Fluxtion server at runtime. Our code generated solutions remove the unnecessary cost, complication and inefficiencies required to integrate a streaming server into your application. We use static analysis to create a meta-model of the calculation dependencies that in turn drives our generated solutions.

In general we try promote [zero cost abstractions](http://matthewfl.com/2114/programming/cost-of-abstractions) in Java. Other languages such as C++ and Rust use zero cost abstraction as a mean to achieving high performance. With Fluxtion we want to bring this paradigm to Java stream processing applications.

Maintenance is the most expensive cost of any system, we believe by generating code, png's, graphML and injecting monitoring points maintenance costs are reduced. Our philosophy is to help a business reduce maintenance by offering visual and documentary insight into the actual business logic.

Please check out our [vision](https://github.com/v12technology/fluxtion/blob/master/VISION.md) for more discussion about where we see Fluxtion's place in the world.

