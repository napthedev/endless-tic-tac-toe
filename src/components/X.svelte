<script>
  import { scale } from "svelte/transition";

  let isVisible = false;

  isVisible = true;
</script>

<div class="w-full h-full relative">
  {#if isVisible}
    <div
      transition:scale={{ duration: 250 }}
      class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 -rotate-45 w-full h-full flex justify-center items-center"
    >
      <div class="w-[10%] h-[85%] bg-red-600 rounded-full" />
    </div>
    <div
      transition:scale={{ duration: 250 }}
      class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 rotate-45 w-full h-full flex justify-center items-center"
    >
      <div class="w-[10%] h-[85%] bg-red-600 rounded-full" />
    </div>
  {/if}
</div>
