---
description: Describe Fluxtion's csv processing capabilities
---

# CSV processing

## Introduction

Fluxtion supports csv processing capabilities as part of the fu-text extension, headline capabilities:

* automatic marshaller generation
* zero gc marshalling
* automatic marshaller bean mapping 
* column mapping by name
* column mapping by index
* comment lines ignore
* header lines ignore
* transient properties 
* partial ragged row data 
* escaped/unescaped handling
* derived values 
* fixed width conversions 
* primitive types 
* CharSequence support 
* Configurable delimiters and eol characters
* Eof handling
* integration as a node within SEP
* error handling 
* error logging
* Validation row and field level
* Validation error logging
* Success and failed notifier
* event sink dispatch
* multi-event input file with type key

## Overview

The goal of CSV processing within fluxtion is to convert a byte stream into a java instance, the instance being a managed node within the SEP. In Fluxtion terms a CSV parse definition creates a node that dependent nodes in the graph can process during an execution wave. The CSV marshaller node receives many char events but only indicates a change when a valid instance is parsed on receipt of the end of record delimiter. In this way dependent nodes only process the execution wave when a fully realised instance is available. The actual instance is encapsulated within a Wrapper node. The wrapper node allows access to the underlying marshalled instance.



## 

