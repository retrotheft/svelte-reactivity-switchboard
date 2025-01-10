<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   import StateDisplay from "../../StateDisplay.svelte";
   // @ts-ignore
   export { contextScript, contextTemplate };
</script>

<!--
   switch connections to an array in order to get individual transitions?
   add hover condition somehow? (user hovers over events, mouse etc.)
-->
{#snippet contextScript(connections: SvelteSet<string>, hidden: boolean)}
   {#if connections.size > 0}
      <article class="snippet spring" class:hidden>
         <p class="html-comment">context</p>
         <p>import <span class="curly">getContext</span> from 'svelte'</p>
         <p>const <span class="curly">{Array.from(connections).filter(c => stateToggles[stateMeta[c]]).join(", ")}</span> = getContext("source")</p>
      </article>
   {/if}
{/snippet}

{#snippet contextTemplate(connections: SvelteSet<string>, hidden: boolean, stateObject: { [key: string]: any })}
   {#if Object.keys(stateObject).length > 0}
      <article class="snippet spring" class:hidden>
         <ul>
            <span class="html-comment">context</span>
            {#each connections as key}
               <StateDisplay {key} value={stateObject[key]} context />
            {/each}
         </ul>
      </article>
   {/if}
{/snippet}