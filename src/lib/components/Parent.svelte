<script lang="ts">
   import type { ActiveMediumsType, ConnectionsType } from "../../App.svelte";
   import { getContext } from "svelte";
   import Source, { getSourceState } from "./Source.svelte";
   import { parentPropsScript, parentPropsTemplate } from "./code-snippets/parent/props.svelte";
   import { parentContextImport, parentContextScript } from "./code-snippets/parent/context.svelte";
   import { parentSnippetTemplate } from "./code-snippets/parent/snippet.svelte";
   
   const { activeMediums, connections }: { activeMediums: ActiveMediumsType; connections: ConnectionsType } = getContext("app");
</script>

<section class="component" id="parent">
   {#each Object.keys(connections["parent-snippet"]) as key}
      {key}
   {/each}
   <section class="script">
      {#if connections["parent-context"].size > 0}
         {@render parentContextImport(connections["parent-context"], !activeMediums["parent-context"])}
      {/if}

      {#if connections["parent-props"].size > 0}
         {@render parentPropsScript(connections["parent-props"], !activeMediums["parent-props"], getSourceState())}
      {/if}

      {#if connections["parent-context"].size > 0}
         {@render parentContextScript(connections["parent-context"], !activeMediums["parent-context"], getSourceState())}
      {/if}
   </section>
   <section class="template">
      {#if connections["parent-props"].size > 0}
         {@render parentPropsTemplate(!activeMediums["parent-props"])}
      {/if}
      {#if connections["parent-snippet"].size > 0}
         {@render parentSnippetTemplate(connections["parent-snippet"], !activeMediums["parent-snippet"], getSourceState())}
      {/if}
   </section>
</section>

<Source />
