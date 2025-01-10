<script lang="ts">
   let { origin }: { origin: { x: number; y: number } } = $props();

   const cursorPos = $state({
      x: origin.x,
      y: origin.y,
   });

   function onmousemove(event: MouseEvent) {
      cursorPos.x = event.clientX;
      cursorPos.y = event.clientY;
   }

   const distance = $derived(Math.hypot(cursorPos.x - origin.x, cursorPos.y - origin.y));
</script>

<svelte:window {onmousemove} />

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_click_events_have_key_events -->
<div class="drawing-area">
   <svg class="line-container">
      <path
         d="M {origin.x},{origin.y} 
            Q {(origin.x + cursorPos.x) / 2},{(origin.y + cursorPos.y) / 2 + distance * 0.3} 
            {cursorPos.x},{cursorPos.y}"
         class="curved-line" />
   </svg>
</div>

<style>
   .drawing-area {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      cursor: crosshair;
      z-index: 1;
      pointer-events: none;
   }

   .line-container {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
   }

   .curved-line {
      fill: none;
      stroke: lightgreen;
      stroke-width: 2px;
   }
</style>
