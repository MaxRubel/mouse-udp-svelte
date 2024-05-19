<script>
  // @ts-nocheck

  import { onDestroy, onMount } from "svelte";

  let x = 0;
  let y = 0;
  const ws = new WebSocket("ws://localhost:8080/ws");

  onMount(() => {
    ws.onopen = () => {
      console.log("Connected to server");
    };

    document.addEventListener("mousemove", (e) => {
      x = e.x;
      y = e.y;
      const userId = 12323;

      let buffer = new ArrayBuffer(8);

      // 00000000 00000000
      // 00000000 00000000
      // 00000000 00000000 00000000 00000000

      let view = new DataView(buffer);

      view.setUint16(0, x, true);

      view.setUint16(2, y, true);

      view.setUint32(4, userId, true);

      ws.send(buffer);
    });
  });

  onDestroy(() => {
    client.close();
  });

  ws.onmessage = (event) => {
    console.log(`Received: ${event.data}`);
  };
</script>

<main>
  <h1>
    <div>
      X: {x}
    </div>
    <div>
      Y:{y}
    </div>
  </h1>
</main>

<style>
</style>
