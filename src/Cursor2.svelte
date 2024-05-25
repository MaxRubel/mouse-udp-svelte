<script>
  export let p2x;
  export let p2y;
</script>

<main>
  <div class="cursor" style="left:{p2x}px; top: {p2y}px;">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="22"
      height="22"
      fill="currentColor"
      class="cursor-icon"
      viewBox="0 0 16 16"
    >
      <path
        d="M14.082 2.182a.5.5 0 0 1 .103.557L8.528 15.467a.5.5 0 0 1-.917-.007L5.57 10.694.803 8.652a.5.5 0 0 1-.006-.916l12.728-5.657a.5.5 0 0 1 .556.103z"
      />
    </svg>
  </div>
</main>

<style>
  .cursor {
    height: 8px;
    width: 8px;
    position: absolute;
    pointer-events: none;
  }

  .cursor-icon {
    transform: translateY(-25px) rotate(-72deg);
  }
</style>
