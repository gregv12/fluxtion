---
description: Description of design time Fluxion utilities
---

# Event stream compiler

An Event Stream Compiler\(ESC\) is the core enabling technology driving Fluxtion. The ESC is a new concept in the event processing landscape, injecting a phase into the developer workflow during compilation. Meta-data is supplied at compile time describing the processing requirements for an event stream. After inspecting the meta-data the ESC generates a set of artifacts collectively known as a Static Event Processor\(SEP\). The generated SEP processes an event stream to meet the described requirements.

A SEP can be integrated into an application as a library, with no additional dependencies. The generated code is a reductionist self contained processing component. Comparing an ESC to a traditional event processing server is analogous to comparing a compiled program with an interpreted version, where the SEP is the compiled instance. The key concepts are:

* Meta-data processing describing the graph and application event stream processing are separated.
* The Event Stream Compiler operates at compile time.
* Meta-data is supplied as well as derived by static analysis.
* Meta data is processed by the Event Stream Compiler to generate a Static Event Processor.
* Only the Static Event Processor is required at runtime to process an event stream.

