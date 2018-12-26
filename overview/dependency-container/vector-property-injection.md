---
description: Injecting scalar properties into a managed node
---

# Vector property injection

## Introduction <a id="introduction"></a>

Fluxtion generator will inject vector properties into the SEP at runtime. The properties are read at generation time and the generated SEP will have the values hard-coded in the generated file. Using property injection client code can inject either default values or constant values into the SEP.

### Property type support <a id="property-type-support"></a>

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

Collection support

The following collections are supported for injection:

* Arrays
* java.util.List 



