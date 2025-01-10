<script lang="ts" module>
   import { type SvelteSet } from "svelte/reactivity";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { childPropsTemplate, parentPropsImport, parentPropsScript };
</script>

{#snippet childPropsTemplate(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">props to child</p>
      <p>
         <span class="component"
            >Child
            {#each connections as connection}
               {#if stateToggles[stateMeta[connection]]}
                  <span class="curly close">{connection}</span>&nbsp;
               {/if}
            {/each}</span>
      </p>
   </article>
{/snippet}

{#snippet parentPropsImport(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">props to parent</p>
      <p>let <span class="curly">transferState</span> = $props()</p>
   </article>
{/snippet}

{#snippet parentPropsScript(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">props to parent</p>
      <p>const <span class="curly">transferState</span> = getContext("parent")</p>
      <ul>
         $effect(() =&gt; &#123;
         <li>
            transferState({Array.from(connections)
               .filter((c) => stateToggles[stateMeta[c]])
               .join(", ")})
         </li>
         <li>
            <span class="js-comment">use $effect with caution. See tool tips for more info.</span>
         </li>
         &#125;)
      </ul>
   </article>
{/snippet}

<style scoped>
   ul li {
      padding-left: var(--tab-size);
      transition: padding-left 0.2s ease-in-out;
   }
</style>
