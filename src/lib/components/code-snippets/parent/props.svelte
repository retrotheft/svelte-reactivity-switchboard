<script lang="ts" module>
   import { type SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { parentPropsScript, parentPropsTemplate };
</script>

{#snippet parentPropsScript(connections: SvelteSet<string>, hidden: boolean, stateObject: Record<string, any>)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">props</p>
      <ul>
         function transferState({Array.from(connections).filter(key => stateToggles[stateMeta[key]]).join(", ")}) &#123;
         {#each connections as key}
            <li><StateDisplay {key} value={stateObject[key]} snippet /></li>
         {/each}
         &#125;
      </ul>
   </article>
{/snippet}

{#snippet parentPropsTemplate(hidden: boolean)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">props</p>
      <p>
         <span class="component"
            >Child
            <span class="curly close">transferState</span>&nbsp;
         </span>
      </p>
   </article>
{/snippet}

<style scoped>
   li {
      padding-left: var(--tab-size);
      transition: padding-left 0.2s ease-in-out;
   }
</style>
