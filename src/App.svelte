<script lang="ts">
  import Camera from './lib/Camera.svelte'
  import SliderPuzzle from './lib/SliderPuzzle.svelte'

  let pic: string
</script>

<main>
  {#if pic}
    <SliderPuzzle {pic} />
  {:else}
    <Camera bind:pic />
  {/if}
</main>
