<script lang="ts" module>
   // @ts-nocheck
   // exporting snippets is still a bit wonky
   export const tooltips = {
      "parent-context": {
         callback: parentContextCallbackTooltip,
      },
      "parent-props": {
         callback: parentPropsCallbackTooltip,
      },
      "parent-snippet": {
         let: parentSnippetLetTooltip,
         const: parentSnippetConstTooltip,
         callback: parentSnippetCallbackTooltip,
      },
      "child-context": {
         const: childContextConstTooltip,
         callback: childContextCallbackTooltip,
      },
      "child-props": {
         let: childPropsLetTooltip,
         const: childPropsConstTooltip,
         callback: childPropsCallbackTooltip,
      },
      "child-snippet": {
         let: childSnippetLetTooltip,
         const: childSnippetConstTooltip,
         callback: childSnippetCallbackTooltip,
      },
   };
</script>

{#snippet parentContextCallbackTooltip()}
   <article class="tooltip">
      <h3>Parent Context Callbacks</h3>
      <p>If you want to pass state upward through context, you'll need to pass a callback down first.</p>
      <p>
         Once you have, you can use the $effect rune to update the parent state, but it's highly advisable to call this callback directly from where the state
         is being updated in the first place. In this app, that would be the mouse event handlers.
      </p>
      <p>From the Svelte Docs page on $effect:</p>
      <blockquote>
         In general, $effect is best considered something of an escape hatch — useful for things like analytics and direct DOM manipulation — rather than a tool
         you should use frequently. In particular, avoid using it to synchronise state.
      </blockquote>
   </article>
{/snippet}

{#snippet parentPropsCallbackTooltip()}
   <article class="tooltip">
      <h3>Parent Props Callbacks</h3>
      <p>If you want to pass state upward through props, you'll need to pass a callback down first.</p>
      <p>
         Once you have, you can use the $effect rune to update the parent state, but it's highly advisable to call this callback directly from where the state
         is being updated in the first place. In this app, that would be the mouse event handlers.
      </p>
      <p>From the Svelte Docs page on $effect:</p>
      <blockquote>
         In general, $effect is best considered something of an escape hatch — useful for things like analytics and direct DOM manipulation — rather than a tool
         you should use frequently. In particular, avoid using it to synchronise state.
      </blockquote>
   </article>
{/snippet}

{#snippet parentSnippetLetTooltip()}
   <article class="tooltip">
      <h3>Parent Snippet Let</h3>
      <p>
         Similarly to slot props previously, snippets allow you to pass state upward to the parent, as a parameter to the snippet. Be aware though that this is
         pretty much a read only arrangement, as Svelte doesn't like it when you try to affect state through the template.
      </p>
   </article>
{/snippet}

{#snippet parentSnippetConstTooltip()}
   <article class="tooltip">
      <h3>Parent Snippet Const</h3>
      <p>
         Similarly to slot props previously, snippets allow you to pass state upward to the parent, as a parameter to the snippet. Be aware though that this is
         pretty much a read only arrangement, as Svelte doesn't like it when you try to affect state through the template.
      </p>
   </article>
{/snippet}

{#snippet parentSnippetCallbackTooltip()}
   <article class="tooltip">
      <h3>Parent Snippet Callbacks</h3>
      <p>
         There are probably some very interesting and wacky use cases for passing a callback up through a snippet. Unfortunately, I've run out of time to
         explore these. Perhaps in a future update!
      </p>
   </article>
{/snippet}

{#snippet childContextConstTooltip()}
   <article class="tooltip">
      <h3>Child Context Const</h3>
      <p>
         Most of the time, if you're planning to pass reactive state through context, you'll want to declare it in an object. Commonly, this is suggested as
         something like:
      </p>
      <ul data-block="script">
         const yourVariable = $state(&#123;
         <li>value: "hello there"</li>
         &#125;)
      </ul>
      <p>
         While this is fine, it does feel a bit arbitrary. You might prefer instead to group several pieces of related state in a component into the same
         object, e.g:
      </p>
      <ul data-block="script">
         const mousePos = $state(&#123;
         <li>x: 0</li>
         <li>y: 0</li>
         &#125;)
      </ul>
      <p>This is functionally equivalent, but feels a bit more purposeful.</p>
      <p>
         Also, while here a distinction has been made between let and const for the purposes of passing state, you can technically declare reactive objects with
         let.
      </p>
   </article>
{/snippet}

{#snippet childContextCallbackTooltip()}
   <article class="tooltip">
      <h3>Child Context Callbacks</h3>
      <p>
         Context isn't reactive by default, so it's not possible to pass state which has been declared at the root level. In general, this means declarations
         made with let, including $state.raw*. Trying to pass state this way will give you the compiler warning:
      </p>
      <p class="warning">State referenced in its own scope will never update. Did you mean to reference it inside a closure? (state_referenced_locally)</p>
      <p>If you want to pass root level reactive state, you need to wrap it in a closure. Alternatively, you can just wrap your state in an object.</p>
      <hr />
      <p>
         * Yes, technically you can declare an object with let and it will work fine. But if you're not planning on changing the reference, you might as well
         use const. Don't @ me.
      </p>
   </article>
{/snippet}

{#snippet childPropsLetTooltip()}
   <article class="tooltip">
      <h3>Child Props Let</h3>
      <p>
         Props are the default and most common way to pass state. They're already reactive, so a good chunk of the time, this is all you need. However, if you
         need to pass something through a chain of components, or skip over a component, you might be better served to use context.
      </p>
   </article>
{/snippet}

{#snippet childPropsConstTooltip()}
   <article class="tooltip">
      <h3>Child Props Const</h3>
      <p>When passing an object as props, you can pass the object, but you can also destructure the object. This can help reduce some code:</p>
      <ul data-block="script">
         const mousePos = $state(&#123;
         <li>x: 0</li>
         <li>y: 0</li>
         &#125;)
      </ul>
      <ul data-block="template">
         <li><span class="angle">Child <span class="curly">...mousePos</span> /</span></li>
      </ul>
      Then, in the child, you can directly access the x and y values:
      <ul data-block="script">
         <li>let <span class="curly">x, y</span> = $props()</li>
      </ul>
   </article>
{/snippet}

{#snippet childPropsCallbackTooltip()}
   <article class="tooltip">
      <h3>Child Props Callbacks</h3>
      <p>Since props are reactive by default anyway, you might wonder why you would pass a callback as props.</p>
      <p>One use case is if you want to embed some functionality in the child by using a function; you can pass relevant state inside a closure.</p>
      <ul>
         <span class="curly">#each array as element, index</span>
         <li><span class="angle">Child remove=<span class="curly">() =&gt; array.splice(index, 1)</span></span></li>
         <span class="curly">/each</span>
      </ul>
      <p>This way, the child is capable of removing itself from the array, without ever needing to know its index number.</p>
      <p>Of course, you could achieve the same thing by passing the remove button as a snippet.</p>
   </article>
{/snippet}

{#snippet childSnippetLetTooltip()}
   <article class="tooltip">
      <h3>Child Snippet Let</h3>
      <p>
         Like slots previously, snippets allow you to declare your template code in the parent component and render it in another component, either parent or
         child.
      </p>
      <p>In this case, since the code for the snippet is in the same component as the state, it's trivial to include state reactively.</p>
   </article>
{/snippet}

{#snippet childSnippetConstTooltip()}
   <article class="tooltip">
      <h3>Child Snippet Const</h3>
      <p>
         Like slots previously, snippets allow you to declare your template code in the parent component and render it in another component, either parent or
         child.
      </p>
      <p>In this case, since the code for the snippet is in the same component as the state, it's trivial to include state reactively.</p>
   </article>
{/snippet}

{#snippet childSnippetCallbackTooltip()}
   <article class="tooltip">
      <h3>Child Snippet Callbacks</h3>
      <p>
         As a corollary to the Child Props Callbacks tooltip, you might use a callback here to provide functionality to the child while limiting the knowledge that the child has access to, such as a remove button in a list. Often, I find this choice is governed by where my scoped CSS styling is.
      </p>
   </article>
{/snippet}

<style scoped>
   article.tooltip {
      font-family: var(--font-family);
      font-size: var(--font-size);
      padding-inline: 2em;
      padding-bottom: 0.5em;
      text-transform: none;
      text-align: left;
      width: 55ch;
      height: auto;
   }

   ul {
      font-family: "Fira Code", monospace;
      font-size: var(--code-size);
      border-top: 1px solid grey;
      border-bottom: 1px solid grey;
      padding-block: 0.5em;
      position: relative;
   }

   ul:before {
      content: attr(data-block);
      position: absolute;
      top: 0;
      right: 0;
      font-size: 0.8em;
      color: grey;
      text-transform: uppercase;
   }

   li {
      padding-left: var(--tab-size);
      /* text-wrap: nowrap; */
      margin-block: 0.5em;
   }

   p.warning {
      color: grey;
   }

   hr,
   hr + p {
      color: grey;
      font-size: 0.9em;
   }

   blockquote {
      border-left: 2px solid grey;
      color: grey;
      padding-left: 1em;
      margin-left: 0;
   }
</style>
