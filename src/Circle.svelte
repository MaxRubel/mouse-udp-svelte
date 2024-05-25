<script>
  export let handleClick;
  export let cx;
  export let cy;
  export let size;
  export let display;
</script>

<main>
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div
    class="circle"
    style="height: {size}px; width: {size}px; left:{cx}px; top: {cy}px; display: {display};"
    on:click={handleClick}
  ></div>
</main>

<style>
  .circle {
    position: absolute;
    cursor: pointer;
    display: block;
    height: 150px;
    width: 150px;
    border-radius: 100%;
    border: none;
    background-color: aquamarine;
  }
  .circle::selection {
    border: none;
  }
</style>
