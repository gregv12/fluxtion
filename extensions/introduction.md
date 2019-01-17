---
description: Extension give Fluxtion additional functionality
---

# Introduction

Fluxtion plugins are available that extend the functionality of the base framework. Each plugin will usually have an api and a builder jar, following the same classpath and naming convention described [here](../overview/building-classpath-and-execution.md#classpath). 

Currently two extensions are developed and released with the core Fluxtion framework to ease developer integration. Eventually the extension versions will be decoupled from the core, the goal to have different release cadences of core and extensions using the maven BOM to declare a compatible version set. The extensions that are presently available for use are:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Extension</th>
      <th style="text-align:left"></th>
      <th style="text-align:left">description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">declarative</td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Provides declarative functionality to define graph nodes.</p>
        <p>Removes the need to imperatively declare a graph.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">group</td>
      <td style="text-align:left">Aggregate data with grouping. Provides derived aggregate
        <br />calculations.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">log</td>
      <td style="text-align:left">Log to consoled with zero gc, triggered by update notifications</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">numeric</td>
      <td style="text-align:left">Numeric functions declaratively described.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">predicate</td>
      <td style="text-align:left">Declarative functional predicates operating on nodes</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">filter</td>
      <td style="text-align:left">FIlters nodes using predicates.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">window</td>
      <td style="text-align:left">WIndow strategies and functions operating on numeric values.</td>
    </tr>
    <tr>
      <td style="text-align:left">fu-text</td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Functional operators for processing ascii text. Builds on the declarative
        framework supplied by declarative extension. All the builder functions
        <br
        />use the declarative form.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">csv</td>
      <td style="text-align:left">Parse, transform, validate, event dispatch for CSV file inputs.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">ascii</td>
      <td style="text-align:left">Ascii pattern matches, extractors and primitive conversions.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">event</td>
      <td style="text-align:left">Event forwarding between SEP's</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">math</td>
      <td style="text-align:left">Basic set of math functions</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">filter</td>
      <td style="text-align:left">text filtering</td>
    </tr>
  </tbody>
</table>



