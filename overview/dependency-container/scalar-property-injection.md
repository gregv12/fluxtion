---
description: Injecting scalar properties into a managed node
---

# Scalar property injection

## Introduction

Fluxtion generator will inject properties into the SEP at runtime. The properties are read at generation time and the generated SEP will have the values hard-coded in the generated file. Using property injection client code can inject either default values or constant values into the SEP. 

### Property type support

The supported types that can be injected into a SEP member are:

* byte
* char
* short
* int
* float
* long
* double
* String
* Enum

### Injection mechanism

