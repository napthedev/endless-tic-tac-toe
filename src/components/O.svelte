<script>
  import { scale } from "svelte/transition";

  let isVisible = false;

  isVisible = true;
</script>

<div class="w-full h-full relative">
  {#if isVisible}
    <div
      transition:scale={{ duration: 250 }}
      class="absolute inset-[15%] w-[70%] h-[70%] border-[0.1em] border-sky-600 rounded-full"
    />
  {/if}
</div>
