<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   // @ts-ignore
   export { snippetScript, snippetTemplate };
</script>

{#snippet snippetScript(hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">snippet</p>
      <p>let <span class="curly">children</span> = $props()</p>
   </article>
{/snippet}

{#snippet snippetTemplate(connections: SvelteSet<string>, hidden: boolean, relevantProps: { [key: string]: any })}
   <!-- {@render children(hidden)} -->
   <article class="snippet spring" class:hidden>
      <ul>
         <span class="html-comment">snippet</span><br />
         <span class="curly"><span style="color: lightcoral;">@render</span> children()</span><br />
         {#each connections as key}
            <StateDisplay {key} value={relevantProps[key]} snippet />
         {/each}
      </ul>
   </article>
{/snippet}
