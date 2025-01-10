<script lang="ts" module>
   import { stateMeta, stateToggles } from "./Source.svelte";
   
   function toTitleCase(str: string): string {
      return str.charAt(0).toUpperCase() + str.slice(1);
   }
</script>

<script lang="ts">
   let { key, value, context = false, snippet = false, hideComments = false } = $props();

   const snippets = {
      let: context ? letStateContext : letState,
      raw: context ? rawStateContext : rawState,
      const: constState,
   };
   // obsolete as context was switched to closures
   const reactivity = {
      // let: context ? "non-reactive" : "reactive",
      // raw: context ? "non-reactive" : "reactive",
      let: "reactive",
      raw: "reactive",
      const: "reactive"
   };
</script>

<!-- props -->
{@render snippets[stateMeta[key as keyof typeof stateMeta] as keyof typeof snippets](
   key,
   value,
   reactivity[stateMeta[key as keyof typeof stateMeta] as keyof typeof reactivity],
)}

<!-- always reactive, no problem-->
{#snippet constState(key: string, value: any)}
   <ul class:hidden={!stateToggles.const} class:snippet>
      {key}:
      {#each Object.entries(value) as [k, v]}
         <li>{k}: <span class="curly">{key}.{k}</span> {#if !hideComments}<span class="html-comment reactive">{v}</span>{/if}</li>
      {/each}
   </ul>
{/snippet}

{#snippet letState(key: string, value: any, reactiveClass = "reactive")}
   <li class:hidden={!stateToggles.let} class:snippet>{key}: <span class="curly">{key}</span> {#if !hideComments}<span class="html-comment {reactiveClass}">{value}</span>{/if}</li>
{/snippet}

{#snippet rawState(key: string, value: any, reactiveClass = "reactive")}
   <ul class:hidden={!stateToggles.raw} class:snippet>
      {key}:
      {#each Object.entries(value) as [k, v]}
         <li>{k}: <span class="curly">{key}.{k}</span> {#if !hideComments}<span class="html-comment {reactiveClass}">{v}</span>{/if}</li>
      {/each}
   </ul>
{/snippet}

{#snippet letStateContext(key: string, value: any, reactiveClass = "reactive")}
   <li class:hidden={!stateToggles.let} class:snippet>{key}: <span class="curly">{key}()</span> {#if !hideComments}<span class="html-comment {reactiveClass}">{value}</span>{/if}</li>
{/snippet}

{#snippet rawStateContext(key: string, value: any, reactiveClass = "reactive")}
   <ul class:hidden={!stateToggles.raw} class:snippet>
      {key}:
      {#each Object.entries(value) as [k, v]}
         <li>{k}: <span class="curly">{key}().{k}</span> {#if !hideComments}<span class="html-comment {reactiveClass}">{v}</span>{/if}</li>
      {/each}
   </ul>
{/snippet}

<style scoped>
   ul li {
      padding-left: var(--tab-size);
      transition: padding-left 0.2s ease-in-out;
   }

   .hidden {
      visibility: hidden;
      height: 0;
      margin-block: 0;
      padding-block: 0;
   }

   .snippet {
      opacity: 0.7;
   }
</style>
