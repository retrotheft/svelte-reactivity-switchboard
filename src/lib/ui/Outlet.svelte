<script lang="ts" module>
   import letOutletSvg from "../../assets/let-outlet.svg?raw";
   import constOutletSvg from "../../assets/const-outlet.svg?raw";
   import callbackOutletSvg from "../../assets/callback-outlet.svg?raw";
   import { stateMeta } from "../components/Source.svelte";

   const svg: { [key: string]: string } = {
      let: letOutletSvg,
      const: constOutletSvg,
      callback: callbackOutletSvg,
   };

   function positionTooltip(node: HTMLElement) {
      const updatePosition = () => {
         const rect = node.getBoundingClientRect();
         const windowHeight = window.innerHeight;
         const elementMiddle = rect.top + (rect.height / 2);
         const isTop = elementMiddle < (windowHeight / 2);
         
         // Add class to the outlet element (node), not the tooltip
         node.classList.toggle('top-half', isTop);
      };

      // Initial position
      updatePosition();

      // Update on resize
      window.addEventListener('resize', updatePosition);

      return {
         destroy() {
            window.removeEventListener('resize', updatePosition);
         }
      };
   }
</script>

<script lang="ts">
   let { name, connections, meta, tooltips } = $props();

   const activeOutlets = $derived.by(() => {
      const array = Array.from(connections);
      const stateMetaArray = array.map((connection) => {
         return stateMeta[connection as keyof typeof stateMeta];
      });
      const outletMetaArray = stateMetaArray.map((state) => {
         return meta[state as keyof typeof meta];
      });
      return outletMetaArray;
   });
</script>

<div class="outlet" class:active={activeOutlets.includes(name)} use:positionTooltip>
   <div class="outlet-tooltip" >
      {@render tooltips?.[name]?.()}
   </div>
   {@html svg[name]}
</div>

<style scoped>
   div.outlet {
      display: flex;
      flex-direction: column;
      justify-content: center;
      /* flex-grow: 1; */
      flex-shrink: 1;
      align-items: center;
      min-height: 20px;
      position: relative;
   }

   div.outlet :global(svg) {
      max-width: 100%;
      height: auto;
   }

   div.outlet :global(svg path:first-child) {
      fill: #f0f0f0;
   }

   div.outlet.active :global(svg path:first-child) {
      fill: lightgreen;
   }

   div.outlet-tooltip {
      position: absolute;
      visibility: hidden;
      transform: translateX(-100%);
      left: -20px;
      height: auto;
      border: 1px solid white;
      background-color: rgba(0, 0, 0.8);
   }

   :global(.top-half div.outlet-tooltip) {
      top: 0;
   }

   :global(:not(.top-half div.outlet-tooltip)) {
      bottom: 0;
   }

   :global(.showTooltips div.outlet:hover div.outlet-tooltip) {
      visibility: visible;
   }
</style>
