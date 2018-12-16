---
description: Instructions used by the developer to configure event processing
---

# Event processing primitives

This section covers the primitives the developer can use in their code to guide the Fluxtion event stream compiler in creating the event dispatch logic within a SEP. In general the primitives take the form of annotations added to methods in the user created nodes. 

Each page will cover a single concept and possibly an accompanying annotation, where appropriate diagrams and pseudo code are employed to help illustrate the concept.

This section does not cover [graph construction](../graph-building-primitives.md), invoking [Fluxtion generator](../../tools/fluxtion-tool.md) or [auditing](../auditing.md) the SEP at runtime.

Annotations provide a significant avenue for providing meta-data to the Fluxtion ESC, the relevant annotations covered in the this section are:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Annotation</th>
      <th style="text-align:left">Comment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>@AfterEvent</code>
      </td>
      <td style="text-align:left">Marks a method to be called in a class when all event processing has completed.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@EventHandler</code>
      </td>
      <td style="text-align:left">Marks a method as an event handler, an entry point to an execution path.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@FilterId</code>
      </td>
      <td style="text-align:left">Marks a field as the default filter for all event handlers in this class</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@Initialise</code>
      </td>
      <td style="text-align:left">An initialisation method that is invoked by the SEP before event processing.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@NoEventReference</code>
      </td>
      <td style="text-align:left">Marks a parent node as a reference only, the child node will be excluded
        from the execution path of NoEventReference node.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@OnBatchEnd</code>
      </td>
      <td style="text-align:left">Marks a method to be invoked by the SEP when a batch ends.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@OnBatchPause</code>
      </td>
      <td style="text-align:left">Marks a method to be invoked by the SEP when a batch is paused and more
        messages are expected.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@OnEvent</code>
      </td>
      <td style="text-align:left">Marks a method to be included in the execution graph. Will be called when
        all of its dependent nodes on the execution path have processed the event.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p></p>
        <p><code>@OnEventComplete</code>
        </p>
      </td>
      <td style="text-align:left">The inverse of OnEvent behaviour, similar to AfterEvent but only if the
        node is on the execution path.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@OnParentUpdate</code>
      </td>
      <td style="text-align:left">Marks a method to receive the Identity of the parent node(s) that have
        updated.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>@TearDown</code>
      </td>
      <td style="text-align:left">A finalisation method that is invoked by the SEP after all event processing.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p></p>
        <p><code>@PushReference</code>
        </p>
      </td>
      <td style="text-align:left">Inverts the execution path order so the child is before the parent in
        the event notification wave</td>
    </tr>
  </tbody>
</table>

