<script>
  // @ts-nocheck

  import { onDestroy, onMount } from "svelte";
  import Circle from "./Circle.svelte";
  import Cursor2 from "./Cursor2.svelte";
  import Cursor1 from "./Cursor1.svelte";
  import Countdown from "../Countdown.svelte";

  export let youArePlayer;
  export let countdown;

  let cx = 0;
  let cy = 0;
  let x = 0;
  let y = 0;
  let size = 0;

  let p1x = 0;
  let p1y = 0;
  let p2x = 0;
  let p2y = 0;

  let scoreO = 0;
  let score1 = 0;

  let oldX = 0;
  let oldY = 0;

  const ws = new WebSocket("ws://localhost:8080/ws");

  onMount(() => {
    ws.onopen = () => {
      console.log("Connected to server");
    };

    let throttleTimer;

    const handleMouseMove = (e) => {
      x = e.x;
      y = e.y;
    };

    const throttledMouseMove = (e) => {
      if (!throttleTimer) {
        handleMouseMove(e);
        throttleTimer = setTimeout(() => {
          throttleTimer = undefined;
        }, 30);
      }
    };

    document.addEventListener("mousemove", throttledMouseMove);
  });

  const archiveMousePos = (x, y) => {
    oldX = x;
    oldY = y;
  };

  const sendMousePos = () => {
    if (oldX === x && oldY === y) {
      return;
    }

    let buffer = new ArrayBuffer(6);
    let view = new DataView(buffer);

    view.setUint8(0, 0, true);
    view.setUint16(1, x, true);
    view.setUint16(3, y, true);
    view.setUint8(5, youArePlayer, true);

    ws.send(buffer);

    archiveMousePos(x, y);
  };

  let intervalId;
  let gameRun = false;

  const circleMoves = (x1, y1) => {
    clearInterval(intervalId);
    if (gameRun) {
      size = 0;
      cx = x1;
      cy = y1;
      intervalId = setInterval(() => {
        if (size < 800) {
          size += 1;
        }
      }, 40);
    }
  };

  const buttonClick = () => {
    gameRun = !gameRun;

    if (!gameRun) {
      clearInterval(intervalId);
    } else {
      circleMoves();
    }
  };

  const handleClick = () => {
    let buffer = new ArrayBuffer(9);
    let view = new DataView(buffer);

    view.setUint8(0, 1, true);
    view.setUint8(1, youArePlayer, true);

    ws.send(buffer);
  };

  let interval;
  interval = setInterval(sendMousePos, 50);

  onDestroy(() => {
    ws.close();
    clearInterval(interval);
  });

  ws.onmessage = (event) => {
    if (event.data[0] === "n") {
      const [, s0, s1] = event.data.split("/");
      scoreO = s0;
      score1 = s1;
    }

    if (event.data[0] === "m") {
      const [, p0, p1, p2, p3] = event.data.split("/");
      p1x = p0;
      p1y = p1;
      p2x = p2;
      p2y = p3;
    }
    if (event.data[0] === "c") {
      const [, px, py] = event.data.split("/");
      circleMoves(px, py);
    }
  };

  const startGame = () => {
    countdown = false;
    gameRun = true;
    circleMoves(300, 300);
  };

  $: console.log("game run:", gameRun);
</script>

<main>
  {#if countdown}
    <Countdown {startGame} />
  {:else}
    <!-- <button on:click={buttonClick}>
      {#if gameRun}
        STOP
      {:else}
        START
      {/if}
    </button>
    <button on:click={handleClick}> test click </button> -->
    <h4>
      <div>Player 1 Score: {scoreO}</div>
      <div>Player 2 Score: {score1}</div>
      <div>You are player: {youArePlayer}</div>
      <!-- <div>
        X: {x}
      </div>
      <div>
        Y:{y}
      </div> -->
      <div>
        <Circle
          {handleClick}
          {cx}
          {cy}
          {size}
          display={gameRun ? "block" : "none"}
        />
        {#if youArePlayer !== 1}
          <Cursor1 {p1x} {p1y} />
        {:else}
          <Cursor2 {p2x} {p2y} />
        {/if}
      </div>
    </h4>
  {/if}
</main>

<style>
</style>
