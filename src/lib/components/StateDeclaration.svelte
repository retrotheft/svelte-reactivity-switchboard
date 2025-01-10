<script lang="ts">
   import { getContext } from "svelte";
   import type { ConnectionPoint } from "../../App.svelte";

   let { name, prop, setChildContext, children, visible } = $props();

   const { registerPoint }: { registerPoint: (point: ConnectionPoint) => void } = getContext("app");
   const { state: childContext }: { state: () => any } = getContext("source");

   const pointRegistration = (e: MouseEvent) => ({
      x: e.clientX,
      y: e.clientY,
      name,
      polarity: 1,
      callback: (targetName: string) => {
         if (targetName === "child-context") setChildContext(name, prop);
      },
   });
</script>

<button
   id={name}
   class="state"
   class:visible
   onclick={(e) => {
      e.stopPropagation();
      registerPoint(pointRegistration(e));
   }}>
   {@render children()}
</button>

<style scoped>
   button {
      background: transparent;
      text-align: left;
   }

   button.visible {
      visibility: visible;
      height: auto;
      padding: 0.25em;
      font-size: 1em;
      margin-block: 0.25em;
   }

   button.state {
      margin-inline: 0;
      margin-block: 0.5em;
   }

   button.state:hover {
      color: lightgreen;
      border: 1px dotted lightgreen;
   }

   button:not(.visible) {
      margin-block: 0;
      height: 0;
      visibility: hidden;
      padding-block: 0;
   }
</style>
