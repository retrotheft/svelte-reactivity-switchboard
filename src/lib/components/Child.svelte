<script lang="ts">
   import type { ActiveMediumsType, ConnectionsType } from "../../App.svelte";
   import { propsScript, propsTemplate } from "./code-snippets/child/props.svelte";
   import { contextScript, contextTemplate } from "./code-snippets/child/context.svelte";
   import { snippetScript } from "./code-snippets/child/snippet.svelte";
   import { getContext } from "svelte";
   import { getSourceState } from "./Source.svelte";

   // currently unused as context was switched to closures so no visible non-reactivity
   const context: { state: () => Record<string, any> } = getContext("source");
   const { activeMediums, connections }: { activeMediums: ActiveMediumsType; connections: ConnectionsType } = getContext("app");

   let { children } = $props();

   const relevantProps = $derived.by(() => {
      const obj: Record<string, any> = {};
      for (const key in getSourceState()) {
         if (connections["child-props"].has(key)) obj[key] = getSourceState()[key];
      }
      return obj;
   });

   const relevantContext = $derived.by(() => {
      const obj: Record<string, any> = {};
      for (const key in getSourceState()) {
         if (connections["child-context"].has(key)) obj[key] = getSourceState()[key];
      }
      return obj;
   });
</script>

<section class="component" id="child">
   <section class="script">
      {#if connections["child-context"].size > 0}
         {@render contextScript(connections["child-context"], !activeMediums["child-context"])}
      {/if}
      {#if connections["child-props"].size > 0}
         {@render propsScript(connections["child-props"], !activeMediums["child-props"])}
      {/if}
      {#if connections["child-snippet"].size > 0}
         {@render snippetScript(!activeMediums["child-snippet"])}
      {/if}
   </section>

   <section class="template">
      {#if connections["child-context"].size > 0}
         {@render contextTemplate(connections["child-context"], !activeMediums["child-context"], relevantContext)}
      {/if}
      {#if connections["child-props"].size > 0}
         {@render propsTemplate(connections["child-props"], !activeMediums["child-props"], relevantProps)}
      {/if}
      {#if connections["child-snippet"].size > 0}
         {@render children()}
      {/if}
   </section>
</section>
