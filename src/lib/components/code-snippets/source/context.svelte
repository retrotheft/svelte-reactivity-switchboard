<script lang="ts" module>
   import type { SvelteSet } from "svelte/reactivity";
   import { stateMeta, stateToggles } from "../../Source.svelte";
   // @ts-ignore
   export { childContextImport, childContextScript, parentContextImport, parentContextScript };

   // transforms let and raw to closures
   function transformContextParams(connections: SvelteSet<string>) {
      const array = Array.from(connections).filter(c => stateToggles[stateMeta[c]]);
      const newArray = array.map(c => {
         if (stateMeta[c] === "let" || stateMeta[c] === "raw") return c + ": () => " + c;
         return c;
      });
      return newArray.join(", ");
   }
</script>

{#snippet childContextImport(connections: SvelteSet<string>, hidden: boolean)}
<article class="snippet bounce" class:hidden>
   <p class="html-comment">context to descendants</p>
   <p>import <span class="curly">setContext</span> from 'svelte'</p>
</article>
{/snippet}

{#snippet childContextScript(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet bounce" class:hidden>
      <p class="html-comment">context to descendants</p>
      <p>setContext("source", <span class="curly">{transformContextParams(connections)}</span>)</p>
   </article>
{/snippet}

{#snippet parentContextImport(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">context to parent</p>
      <p>import <span class="curly">getContext</span> from 'svelte'</p>
   </article>
{/snippet}

{#snippet parentContextScript(connections: SvelteSet<string>, hidden: boolean)}
   <article class="snippet spring" class:hidden>
      <p class="html-comment">context to parent</p>
      <p>const <span class="curly">transferState</span> = getContext("parent")</p>
      <ul>$effect(() =&gt; &#123;
         <li>transferState({Array.from(connections).join(", ")})</li>
         <li><span class="js-comment">check Svelte docs for info on when not to use $effect()</span></li>
      &#125;)</ul>
   </article>
{/snippet}

<style scoped>
   ul li {
      padding-left: var(--tab-size);
      transition: padding-left 0.2s ease-in-out;
   }
</style>