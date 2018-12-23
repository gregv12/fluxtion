---
description: Invoking Fluxtion compiler from maven
---

# Maven plugin

## Introduction

Invoking Fluxtion as a standalone tool can have various difficulties attached to it. Wrapping the use of Fluxtion as a maven plugin has the following advantages:

* Fluxtion generation becomes part of standard build process.
* No need to construct complex command line arguments.
* Input and output files reside in standard locations.
* Automation reduces developer effort and sources of error.

## Executing Fluxtion in maven

The tool steps a user should follow to execute the fluxtion event stream compiler via maven are :

* Create a maven project declaring Fluxtion libraries as dependencies
* Write application classes representing nodes and events to fulfill [event processing](https://fluxtion.gitbook.io/docs/overview/child-2) requirement.
* Create meta-data describing the [construction of the execution graph](https://fluxtion.gitbook.io/docs/overview/graph-building-primitives).
* Add the fluxtion maven plugin to the build plugins, setting configuration.
* Run mvn install.

### Fluxtion dependencies

Fluxtion has two libraries the user needs to declare in dependency section, they are described [here](fluxtion-tool.md#classpath). As described the classpath section the builder is not required in the final application. To remove the builder from the runtime set of dependencies specify the provided scope. An example valid dependency section:

```markup
    <dependencies>
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-api</artifactId>
            <version>[FLUXTION VERSION]</version>
        </dependency>
        <dependency>
            <groupId>com.fluxtion</groupId>
            <artifactId>fluxtion-builder</artifactId>
            <scope>provided</scope>
            <version>[FLUXTION VERSION]</version>
        </dependency>
    </dependencies>
```

### Develop SEP processor

Application event processing and graph description are covered in the [event processing](../overview/child-2/) and [graph building](../overview/graph-building-primitives/) section.

Build plugins



