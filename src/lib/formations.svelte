<script>
  export let formation = [];
  export let positions = {};
</script>

<div class="relative w-[600px] h-[900px] bg-green-700 rounded-lg border-4 border-green-900 shadow-lg mx-auto">
  {#each formation as posKey}
    {#if positions[posKey]}
      <div
        class="absolute bg-gray-900 bg-opacity-90 rounded-md px-3 py-1 font-semibold text-center shadow-md select-none cursor-default transform -translate-x-1/2 -translate-y-1/2"
        style="left: {positions[posKey].x}%; top: {positions[posKey].y}%;"
        title="{positions[posKey].name}"
      >
        {posKey}
      </div>
    {/if}
  {/each}
</div>