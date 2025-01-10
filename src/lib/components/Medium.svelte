<script lang="ts" module>
   import { tooltips } from "../ui/Tooltips.svelte";

   const shortName: { [key: string]: string } = {
      context: "Ctx",
      props: "Prop",
      snippet: "Snip",
   };

   const mediumMeta = {
      "parent-context": {
         let: "callback",
         const: "callback",
         raw: "callback",
      },
      "parent-props": {
         let: "callback",
         const: "callback",
         raw: "callback",
      },
      "parent-snippet": {
         let: "let",
         const: "const",
         raw: "let",
      },
      "child-context": {
         let: "callback",
         const: "const",
         raw: "callback",
      },
      "child-props": {
         let: "let",
         const: "const",
         raw: "let",
      },
      "child-snippet": {
         let: "let",
         const: "const",
         raw: "let",
      },
   };
</script>

<script lang="ts">
   import Outlet from "../ui/Outlet.svelte";
   import { getContext } from "svelte";
   import type { ConnectionsType, ConnectionPoint } from "../../App.svelte";

   let { name, classes, active = $bindable(), reset, outlets = ["let", "const", "callback"] } = $props();

   const { connections, registerPoint }: { connections: ConnectionsType; registerPoint: (point: ConnectionPoint) => void } = getContext("app");

   const pointRegistration = (event: MouseEvent) => ({
      x: event.clientX,
      y: event.clientY,
      name,
      polarity: -1,
      callback: (_: string) => {},
   });
</script>

<div class={["external", classes].join(" ")}>
   <span data-name={name.split("-")[1]} data-short={shortName[name.split("-")[1]]}>&nbsp;</span>
   <div class="internal">
      <button class="control power" aria-label="power" class:active onclick={() => (active = !active)}>{active ? "On" : "Off"}</button>
      <button class="control reset" aria-label="clear" onclick={reset}></button>
      <button
         class="connect"
         class:active
         data-name={name}
         aria-label="connect"
         onclick={(e) => {
            e.stopPropagation();
            registerPoint(pointRegistration(e));
         }}>
         {#each outlets as outlet}
            <Outlet name={outlet} connections={connections[name as keyof ConnectionsType]} meta={mediumMeta[name as keyof typeof mediumMeta]} tooltips={tooltips[name as keyof typeof tooltips]} />
         {/each}
      </button>
   </div>
</div>

<style scoped>
   div.external {
      position: absolute;
      left: 0;
      height: clamp(100px, 23cqh, 100%);
      flex-grow: 1;
      transform: translateX(-50%);
      width: 3cqw;
      padding: 0;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      font-size: 0.9em;
   }

   div.internal {
      --border-edges: oklch(40% 0 0);
      --border-center: oklch(65% 0 0);
      border: 1px dashed grey;
      border-image: linear-gradient(180deg, var(--border-edges) 0%, var(--border-center) 70%, var(--border-edges) 100%) 1;
      border-style: solid;
      border-width: 2px;
      display: flex;
      flex-direction: column;
      flex-grow: 1;
   }

   span {
      color: grey;
      font-weight: bold;
      text-align: center;
      text-transform: uppercase;
      display: flex;
      justify-content: center;
      overflow: hidden;
      container-type: inline-size;
      position: relative;
      min-width: 3ch;
   }

   span:before {
      content: attr(data-short);
      position: absolute;
      inset: 0;
      @container (inline-size > 7ch) {
         content: attr(data-name);
      }
   }

   button.control {
      /* height: 3em; */
      color: lightcoral;
      border-radius: 0;
      display: flex;
      justify-content: center;
      position: relative;
      container-type: inline-size;
   }

   button.control.power {
      border: 1px solid black;
      /* background-color: hsla(0, 79%, 72%, 0.8); */
      /* transition: color 0.2s ease-in-out; */
      min-height: 20px;
      color: lightcoral;
      font-weight: bold;
      font-size: 0.7rem;
   }

   button.control.power.active {
      /* background-color: hsla(120, 73%, 75%, 0.8); */
      color: lightgreen;
   }

   button.control.power:hover,
   button.control.reset:hover {
      background-color: #444;
   }

   button.control.power:active,
   button.control.reset:active {
      color: white;
   }

   button.control.reset {
      font-size: 0.7rem;
      color: cornflowerblue;
   }

   button.control.reset:before {
      content: "Clear";
      position: absolute;
      inset: 0;
      @container (width < 3ch) {
         content: "Clr";
      }
   }

   button.active {
      color: lightgreen;
   }

   button {
      min-width: 0;
      width: 100%;
      font-size: 0.7rem;
      text-transform: uppercase;
   }

   button:hover {
      color: white;
   }

   button.connect:hover {
      border-color: lightgreen;
   }

   button.connect {
      display: flex;
      flex-direction: column;
      align-items: stretch;
      justify-content: space-evenly;
      flex-grow: 1;
      gap: 0.5em;
      padding: 5px;
      background-color: rgba(0, 0, 0, 0.5);
      opacity: 0.3;
      transition: opacity 0.2s ease-in-out;
      container-type: size;
      container-name: medium;
      /* border: 2px solid yellow; */
   }

   button.connect.active {
      opacity: 1;
   }
   /* 
   button.isConnecting {
      background-color: lightgreen;
   } */

   .parent {
      grid-column: parent;
   }

   .child {
      grid-column: child;
   }

   .context {
      grid-row-start: 2;
      grid-row-end: 3;
   }

   .props {
      grid-row-start: 3;
      grid-row-end: 4;
   }

   .snippet {
      grid-row-start: 4;
      grid-row-end: 5;
   }
</style>
