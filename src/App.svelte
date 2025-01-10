<script lang="ts" module>
   const MEDIUM_KEYS = ["parent-props", "parent-context", "parent-snippet", "child-props", "child-context", "child-snippet"] as const;
   export type MediumType = (typeof MEDIUM_KEYS)[number];

   export type ActiveMediumsType = { [key in MediumType]: boolean };
   export type ConnectionsType = { [key in MediumType]: SvelteSet<string> };

   export type ConnectionPoint = {
      name: MediumType;
      x: number;
      y: number;
      polarity: number;
      callback: (targetName: string) => void;
   };
</script>

<script lang="ts">
   import Parent from "./lib/components/Parent.svelte";
   import Medium from "./lib/components/Medium.svelte";
   import Arc from "./lib/components/Arc.svelte";
   import { setContext } from "svelte";
   import { SvelteSet } from "svelte/reactivity";
   import { resetChildContext, stateMeta } from "./lib/components/Source.svelte";

   let fontSize = $state(1.2);
   let codeSize = $state(0.9);
   let tabSize = $state(2);
   let showTooltips = $state(false);
   let mouseY = $state(0);
   let serif = $state(true);

   const connections = Object.fromEntries(MEDIUM_KEYS.map((key) => [key, new SvelteSet<string>()]));
   const activeMediums = $state(Object.fromEntries(MEDIUM_KEYS.map((key) => [key, true])));

   let currentOrigin = $state.raw<ConnectionPoint | undefined>(undefined);

   function registerPoint(point: ConnectionPoint) {
      if (!currentOrigin) return (currentOrigin = point);
      const polarity = currentOrigin.polarity + point.polarity;
      if (polarity === 0) finaliseConnection(currentOrigin, point);
      currentOrigin = undefined;
   }

   function finaliseConnection(origin: ConnectionPoint, target: ConnectionPoint) {
      const keys: [MediumType, string] | [string, MediumType] = [origin.name, target.name];
      const connectionKey = MEDIUM_KEYS.find((key) => keys.includes(key));
      if (!connectionKey) return;
      const [stateKey] = keys.filter((key) => key !== connectionKey);
      const connectionsSet = connections[connectionKey];
      connectionsSet.add(stateKey);
      origin.callback(target.name);
      target.callback(target.name);
   }

   setContext("app", { activeMediums, connections, registerPoint });

   const resizeObserver = new ResizeObserver(() => (currentOrigin = undefined));
   resizeObserver.observe(document.body);
</script>

<svelte:window onclick={() => (currentOrigin = undefined)} onmousemove={(e) => (mouseY = e.clientY)} />

{#if currentOrigin}
   <Arc origin={currentOrigin} />
{/if}
<main
   style={`--tab-size: ${tabSize}ch; --font-size: ${fontSize}rem; --code-size: ${codeSize}rem; --mouse-y: ${mouseY}px; --font-family: ${serif ? '"EB Garamond", serif' : '"system-ui", sans-serif'};`}
   class:showTooltips>
   <section id="hero">
      <img src="images/svelte-logo-large.png" alt="Svelte" />
      <h1>Reactivity Switchboard</h1>
   </section>
   <section id="help">
      <p>An interactive cheatsheet to visualise how to pass reactive state around your Svelte app.</p>
      <p>To begin, click on a state variable declaration, and connect it to another component via context, props or snippets.</p>
      <p>For more info, turn on tooltips and hover over each outlet.</p>
   </section>
   <section id="settings">
      <ul>
         <li>font type: <button onclick={() => (serif = !serif)}>{serif ? "serif" : "sans-serif"}</button></li>
         <li>
            font size:
            <button onclick={() => (fontSize = Math.max(0.5, Number((fontSize - 0.05).toFixed(2))))}>-</button>
            <span class="between-buttons">{fontSize.toFixed(2)}rem</span>
            <button onclick={() => (fontSize = Math.min(2, Number((fontSize + 0.05).toFixed(2))))}>+</button>
         </li>
         <li>
            code size:
            <button onclick={() => (codeSize = Math.max(0.5, Number((codeSize - 0.05).toFixed(2))))}>-</button>
            <span class="between-buttons">{codeSize.toFixed(2)}rem</span>
            <button onclick={() => (codeSize = Math.min(2, Number((codeSize + 0.05).toFixed(2))))}>+</button>
         </li>
         <li>
            tab-width:
            <button onclick={() => (tabSize = Math.max(0, Math.round(tabSize - 1)))}>-</button>
            <span class="between-buttons">{tabSize.toFixed(0)}ch</span>
            <button onclick={() => (tabSize = Math.min(8, Math.round(tabSize + 1)))}>+</button>
         </li>
         <li>
            tool tips:
            <button onclick={() => (showTooltips = !showTooltips)}>
               {showTooltips ? "on" : "off"}
            </button>
         </li>
      </ul>
   </section>
   <Parent />
   <Medium
      classes="parent context"
      name="parent-context"
      bind:active={activeMediums["parent-context"]}
      reset={() => connections["parent-context"].clear()}
      outlets={["callback"]} />
   <Medium
      classes="parent props"
      name="parent-props"
      bind:active={activeMediums["parent-props"]}
      reset={() => connections["parent-props"].clear()}
      outlets={["callback"]} />
   <Medium classes="parent snippet" name="parent-snippet" bind:active={activeMediums["parent-snippet"]} reset={() => connections["parent-snippet"].clear()} />
   <Medium
      classes="child context"
      name="child-context"
      bind:active={activeMediums["child-context"]}
      reset={() => {
         resetChildContext();
         connections["child-context"].clear();
      }}
      outlets={["const", "callback"]} />
   <Medium classes="child props" name="child-props" bind:active={activeMediums["child-props"]} reset={() => connections["child-props"].clear()} />
   <Medium classes="child snippet" name="child-snippet" bind:active={activeMediums["child-snippet"]} reset={() => connections["child-snippet"].clear()} />
</main>

<style>
   @font-face {
      font-family: "DM Serif Display";
      src: url("/fonts/DMSerifDisplay-Regular.ttf");
   }

   @font-face {
      font-family: "EB Garamond";
      src: url("/fonts/EBGaramond-VariableFont_wght.ttf");
   }
   @font-face {
      font-family: "Fira Mono";
      src: url("/fonts/FiraMono-Regular.ttf");
      font-weight: 400;
   }

   @font-face {
      font-family: "Fira Mono";
      src: url("/fonts/FiraMono-Medium.ttf");
      font-weight: 500;
   }

   @font-face {
      font-family: "Fira Mono";
      src: url("/fonts/FiraMono-Bold.ttf");
      font-weight: 700;
   }

   main {
      font-size: var(--font-size);
      transition: font-size 0.1s linear;
      min-height: 700px;
      display: grid;
      justify-items: center;
      align-items: center;
      grid-template-rows: repeat(4, 1fr);
      grid-template-columns: 1fr [parent] 1fr [child] 1fr;
      font-family: monospace;
      container-type: size;
      container-name: main;
      flex-grow: 1;
      position: relative;
      padding: 1em;
   }

   :global(section.component) {
      /* border: 5px solid transparent; */
      --border-edges: oklch(40% 0 0);
      --border-center: oklch(45% 0 0);
      font-family: "Fira Mono", monospace;
      font-size: var(--code-size);
      transition: font-size 0.1s linear;
      padding: 1rem;
      margin: 1rem;
      width: 30cqw;
      height: 100%;
      grid-row-start: 2;
      grid-row-end: 6;
      overflow: hidden;
      overflow-y: auto;
      /* border-color: #606060; */
      border-image: linear-gradient(90deg, var(--border-edges) 0%, var(--border-center) 50%, var(--border-edges) 100%) 1;
      border-style: solid;
      border-width: 1px;
      background: linear-gradient(180deg, oklch(0% 0 0) 0%, oklch(12% 0 0) 100%);
      position: relative;
   }

   :global(section.component::before) {
      padding: 0.5em;
      position: absolute;
      top: 0;
      right: 0;
      color: grey;
      text-transform: uppercase;
      font-size: var(--code-size);
      font-weight: 500;
      opacity: 0.75;
      letter-spacing: 0.05em;
   }

   :global(section.component:nth-of-type(3)::before) {
      content: "Anywhere";
   }

   :global(section.component#parent:before) {
      content: "Parent";
   }

   :global(section.component#source:before) {
      content: "Source";
   }

   :global(section.component#child:before) {
      content: "Child";
   }

   section#hero {
      grid-row: 1;
      grid-column: 1;
      width: 25cqw;
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 2em;
      position: relative;

      h1 {
         font-size: 3.2rem;
      }
   }

   section#help {
      grid-row: 1;
      grid-column: 2;
      width: 25cqw;
      display: flex;
      justify-content: center;
      align-items: center;
      font-family: var(--font-family);
      color: hsl(220, 0%, 100%);
      font-size: var(--font-size);
      display: block;
      background-color: rgba(0, 0, 0, 0.3);
      padding-inline: 1em;
      border-radius: 1em;
      transition: font-size 0.1s linear;
   }

   section#hero h1 {
      font-family: "DM Serif Display";
      color: hsl(220, 3%, 80%);
   }

   section#settings {
      background-color: rgba(0, 0, 0, 0.3);
      padding: 1em;
      border-radius: 1em;
      font-size: 0.7em;

      li {
         display: flex;
         align-items: center;
         gap: 1em;
         margin-block: 0.25em;
      }

      button {
         border: 1px solid #555;
         padding: 0.35em 0.75em;
      }

      button:not(span + button):not(:has(+ span)) {
         flex-grow: 1;
      }
   }

   /* just use flex */
   span.between-buttons {
      display: inline-block;
      margin-inline: auto;
      width: auto;
   }
</style>
