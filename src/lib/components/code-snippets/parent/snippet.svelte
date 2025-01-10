<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { parentSnippetTemplate };
</script>

{#snippet parentSnippetTemplate(connections: SvelteSet<string>, hidden: boolean, stateObject: Record<string, any>)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">snippet</p>
      <span class="curly close">#snippet parentSnippet({Array.from(connections).filter(key => stateToggles[stateMeta[key]]).join(", ")})</span><br />
      <ul>
         {#each connections as key}
            <StateDisplay {key} value={stateObject[key]} />
         {/each}
      </ul>
      <span class="curly close">/snippet</span>
   </article>
{/snippet}

<style scoped>
   ul {
      margin-block: 0;
   }
</style>
