<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { parentContextImport, parentContextScript };
</script>

{#snippet parentContextImport(connections: SvelteSet<string>, hidden: boolean)}
<article class="snippet bounce" class:hidden>
   <p class="html-comment">context</p>
   <p>import <span class="curly">setContext</span> from 'svelte'</p>
</article>
{/snippet}

{#snippet parentContextScript(connections: SvelteSet<string>, hidden: boolean, stateObject: Record<string, any>)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">context</p>
      <ul>function transferState({Array.from(connections).filter(key => stateToggles[stateMeta[key]]).join(", ")}) &#123;
         <!-- this could just be each individual connection as a parameter, no need for function body -->
         {#each connections as key}
            <li><StateDisplay {key} value={stateObject[key]} snippet /></li>
            <!-- <li>{ connection }: <span class="curly close">{ connection }</span> <span class="html-comment reactive">{JSON.stringify(stateObject[connection])}</span></li> -->
         {/each}
      &#125;</ul>
      <p>setContext("parent", <span class="curly">transferState</span>)</p>
   </article>
{/snippet}

<style scoped>
   li {
      padding-left: var(--tab-size);
      transition: padding-left 0.2s ease-in-out;
   }
</style>
