---
description: Description of build and run time environments
---

# Building, classpath and execution

## Introduction

Fluxtion is a code generator that increases developer efficiency, reduces bugs and makes maintenance easier. Our primary focus is processing a stream of application events resulting in execution of application logic. In this section we cover the plumbing aspects of Fluxtion in a Java environment:

* Build time vs run time
* Classpaths
* executing a solution

## Build time 

Fluxtion carries out analysis on the application classes using configuration the developer provides. This process occurs separately to executing the solution, but is a necessary first step otherwise no [SEP ](developer/static-event-processor/)solution exists.

