<script lang="ts" module>
   export const stateMeta: Record<string, string> = {
      events: "let",
      mouse: "const",
      lastClick: "raw",
   };

   const childContext = $state<Record<string, any>>({});

   export function resetChildContext() {
      delete childContext.events;
      delete childContext.mouse;
      delete childContext.lastClick;
   }

   let events = $state(0);

   const mouse = $state({
      x: 0,
      y: 0,
   });

   // lastClick needs snippet to be generalised
   // also needs context object to correctly identify
   // that it's non-reactive.
   let lastClick = $state.raw({
      x: 0,
      y: 0,
   });

   const stateSets = $derived.by(() => {
      return {
         on: Object.keys(stateToggles).filter((key) => stateToggles[key]),
         off: Object.keys(stateToggles).filter((key) => !stateToggles[key]),
      };
   });

   export function getSourceState(): Record<string, any> {
      return {
         events,
         mouse,
         lastClick,
      };
   }

   export const stateToggles = $state<Record<string, boolean>>({
      let: true,
      const: true,
      raw: true,
   });
</script>

<script lang="ts">
   import { getContext, setContext } from "svelte";
   import { SvelteSet } from "svelte/reactivity";
   import { type ActiveMediumsType } from "../../App.svelte";
   import StateDeclaration from "./StateDeclaration.svelte";
   import Child from "./Child.svelte";
   import { childPropsTemplate, parentPropsImport, parentPropsScript } from "./code-snippets/source/props.svelte";
   import { childContextImport, childContextScript, parentContextImport, parentContextScript } from "./code-snippets/source/context.svelte";
   import { childSnippetTemplate, parentSnippetScript, parentSnippetTemplate } from "./code-snippets/source/snippet.svelte";
   import { snippetTemplate } from "./code-snippets/child/snippet.svelte";

   const {
      activeMediums,
      connections,
   }: {
      activeMediums: ActiveMediumsType;
      mediumClick: (event: MouseEvent, name: string, _polarity: number, callback?: (medium?: string) => void) => void;
      connections: Record<string, SvelteSet<string>>;
   } = getContext("app");

   function onmousedown(event: MouseEvent) {
      const click = {
         x: event.clientX,
         y: event.clientY,
      };
      lastClick = click;
   }

   function onmousemove(event: MouseEvent) {
      events++;
      mouse.x = event.clientX;
      mouse.y = event.clientY;
   }

   export function resetContext() {
      delete childContext.events;
      delete childContext.mouse;
      delete childContext.lastClick;
   }

   function setChildContext(property: string, value: any) {
      childContext[property] = value;
   }

   // this warning will always trigger as the app is demonstrating
   // non-reactivity caused by this warning, i.e. it's intentional
   // svelte-ignore state_referenced_locally
   setContext("source", { state: () => childContext });

   const relevantSnippetProps = $derived.by(() => {
      const obj: Record<string, any> = {};
      for (const key in getSourceState()) {
         if (connections["child-snippet"].has(key)) obj[key] = getSourceState()[key];
      }
      return obj;
   });
</script>

<svelte:window {onmousemove} {onmousedown} />

<section class="component" id="source">
   <div id="state-toggles">
      $state()
      <button onclick={() => (stateToggles.let = !stateToggles.let)} class:on={stateToggles.let}>let</button>
      <button onclick={() => (stateToggles.const = !stateToggles.const)} class:on={stateToggles.const}>const</button>
      <button onclick={() => (stateToggles.raw = !stateToggles.raw)} class:on={stateToggles.raw}>raw</button>
   </div>
   <section class="script">
      {#if connections["parent-context"].size > 0}
         {@render parentContextImport(connections["parent-context"], !activeMediums["parent-context"])}
      {/if}
      {#if connections["child-context"].size > 0}
         {@render childContextImport(connections["child-context"], !activeMediums["child-context"])}
      {/if}

      {#if connections["parent-snippet"].size > 0}
         {@render parentSnippetScript(connections["parent-snippet"], !activeMediums["parent-snippet"])}
      {/if}
      {#if connections["parent-props"].size > 0}
         {@render parentPropsImport(connections["parent-props"], !activeMediums["parent-props"])}
      {/if}

      <StateDeclaration name="events" prop={events} {setChildContext} visible={stateToggles.let}>
         let events = $state({events.toFixed(0)})
      </StateDeclaration>
      <StateDeclaration name="mouse" prop={mouse} {setChildContext} visible={stateToggles.const}>
         const mouse = $state(&#123;
         <p class="code-block">
            x: {mouse.x},<br />
            y: {mouse.y}
         </p>
         &#125;)
      </StateDeclaration>
      <StateDeclaration name="lastClick" prop={lastClick} {setChildContext} visible={stateToggles.raw}>
         let lastClick = $state.raw(&#123;
         <p class="code-block">
            x: {lastClick.x},<br />
            y: {lastClick.y}
         </p>
         &#125;)
      </StateDeclaration>
      {#if connections["child-context"].size > 0}
         {@render childContextScript(connections["child-context"], !activeMediums["child-context"])}
      {/if}
      {#if connections["parent-context"].size > 0}
         {@render parentContextScript(connections["parent-context"], !activeMediums["parent-context"])}
      {/if}
      {#if connections["parent-props"].size > 0}
         {@render parentPropsScript(connections["parent-props"], !activeMediums["parent-props"])}
      {/if}
   </section>
   <section class="template">
      {#if connections["child-props"].size > 0}
         {@render childPropsTemplate(connections["child-props"], !activeMediums["child-props"])}
      {/if}
      {#if connections["child-snippet"].size > 0}
         {@render childSnippetTemplate(connections["child-snippet"], !activeMediums["child-snippet"], relevantSnippetProps)}
      {/if}
      {#if connections["parent-snippet"].size > 0}
         {@render parentSnippetTemplate(connections["parent-snippet"], !activeMediums["parent-snippet"])}
      {/if}
   </section>
</section>

<Child>
   {#if connections["child-snippet"].size > 0}
      {@render snippetTemplate(connections["child-snippet"], !activeMediums["child-snippet"], relevantSnippetProps)}
   {/if}
</Child>

<style scoped>
   section.component {
      padding-top: 0;
   }

   #state-toggles {
      font-family: "Fira Code", monospace;
      color: grey;
      button {
         text-transform: uppercase;
         padding-inline: 0.5em;
         color: #555;
         background: transparent;
      }

      button.on {
         color: white;
      }
   }
</style>
