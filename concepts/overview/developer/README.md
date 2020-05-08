---
description: Fundamental concepts of Fluxtion explained
---

# Core concepts

## Introduction

The pages in this section cover the core concepts of the Fluxtion universe. You will learn about the core runtime component that an application interacts with, a Static Event Processor\(SEP\), how it is created and how to use it to process application event streams. 

You will learn about the two major components in Fluxtion

#### Event stream compiler

Analyses the application components and user supplied configuration to generate a Static Event Processor. The event stream compiler\(ESC\), operates at build time only. The Fluxtion generator is an implementation of an ESC, developers interact with the Fluxtion generator directly or via tools such as a maven plugin.

#### Static event processor

A Static Event Processor \(SEP\) processes a stream of application events at runtime. Developers integrate the SEP into application code. The SEP is the output of the event stream compiler processing.







