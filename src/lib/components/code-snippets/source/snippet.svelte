<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import StateDisplay from "../../StateDisplay.svelte";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { childSnippetTemplate, parentSnippetScript, parentSnippetTemplate };
</script>

{#snippet childSnippetTemplate(connections: SvelteSet<string>, hidden: boolean, relevantSnippetProps: { [key: string]: any })}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">snippet to child</p>
      <span class="angle">Child</span>
      <ul>
         {#each connections as key}
            <StateDisplay {key} value={relevantSnippetProps[key]} hideComments />
         {/each}
      </ul>
      <span class="angle"><span style="color: grey;">/</span>Child</span>
   </article>
{/snippet}

{#snippet parentSnippetScript(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">snippet to parent</p>
      <p>let <span class="curly">parentSnippet</span> = $props()</p>
   </article>
{/snippet}

{#snippet parentSnippetTemplate(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">snippet to parent</p>
      <p><span class="curly close">@render parentSnippet({Array.from(connections).filter(key => stateToggles[stateMeta[key]]).join(", ")})</span></p>
   </article>
{/snippet}

<style scoped>
   ul {
      padding-left: var(--tab-size);
      margin-block: 0;
   }
</style>
