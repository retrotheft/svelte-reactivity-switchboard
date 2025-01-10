<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { propsScript, propsTemplate };
</script>

{#snippet propsScript(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">props</p>
      <p>let <span class="curly">{Array.from(connections).filter(c => stateToggles[stateMeta[c]]).join(", ")}</span> = $props()</p>
   </article>
{/snippet}

{#snippet propsTemplate(connections: SvelteSet<string>, hidden: boolean, relevantProps: { [key: string]: any })}
   {#if Object.keys(relevantProps).length > 0}
      <article class="snippet spring" class:hidden>
         <ul>
            <span class="html-comment">props</span>
            {#each connections as key}
               <StateDisplay {key} value={relevantProps[key]} />
            {/each}
         </ul>
      </article>
   {/if}
{/snippet}
