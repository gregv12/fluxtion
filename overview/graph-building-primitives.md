# Graph building primitives



Annotations provide a significant avenue for providing meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

* `@Config`: A key value pair for configuring an injected instance.
* `@ConfigVariable`: Marks a field as providing configuration for an injected instance.
* `@Inject`: Marks a reference to be created by a [NodeFactory](https://github.com/v12technology/fluxtion/blob/master/builder/src/main/java/com/fluxtion/api/node/NodeFactory.java) and injected by Fluxtion ESC.

