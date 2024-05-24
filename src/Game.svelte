<script>
// @ts-nocheck

import { onDestroy, onMount } from "svelte";
import Circle from "./Circle.svelte";
import Cursor1 from "./Cursor1.svelte";
import ChoosePlayer from "./ChoosePlayer.svelte";

export let youArePlayer;

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
let score1 = 0

let oldX = 0
let oldY = 0

const ws = new WebSocket("ws://localhost:8080/ws");
const playerNo = 2;

onMount(() => {
    ws.onopen = () => {
    console.log("Connected to server");
    };
    
    document.addEventListener("mousemove", (e) => {
    x = e.x;
    y = e.y;
    });
});

const archiveMousePos = (x, y) => {
    oldX = x
    oldY = y
}

const sendMousePos = () => {

if (oldX === x && oldY === y){
    return
}

let buffer = new ArrayBuffer(6);
let view = new DataView(buffer);

view.setUint8(0, 0, true);
view.setUint16(1, x, true);
view.setUint16(3, y, true);
view.setUint8(5, youArePlayer, true); 

ws.send(buffer);

archiveMousePos(x, y)
};

let intervalId;
let gameRun = false;

const citcleMoves = () => {
if(gameRun){
    size = 0
    cx = Math.floor(Math.random() * 1020) + 1;
    cy = Math.floor(Math.random() * 800) + 1;
    intervalId = setInterval(() => {
    if (size < 300){
        size +=1    
    }
}, 200)
} else {
    clearInterval(intervalId)
}
}

const buttonClick = () => {
gameRun = !gameRun

if(!gameRun){
    clearInterval(intervalId);
} else {
    citcleMoves();
}
}

const handleClick = () => {

let buffer = new ArrayBuffer(9);
let view = new DataView(buffer);

view.setUint8(0, 1, true)
view.setUint8(1, youArePlayer, true)

ws.send(buffer);
}

let interval;

interval = setInterval(sendMousePos, 50);

onDestroy(() => {
ws.close();
clearInterval(interval);
});

ws.onmessage = (event) => {

if (event.data[0] === "n"){
    const [,s0, s1] = event.data.split('/')
    scoreO = s0
    score1 = s1
    citcleMoves();
}

if (event.data[0]=== "m"){
    const [,p0, p1, p2,p3] = event.data.split('/')
    p1x = p0;
    p1y = p1;
    p2x = p2;
    p2y = p3;
}
};

</script>
  
  <main>
    <button on:click={buttonClick}> 
      {#if gameRun}
      STOP
      {:else}
      START
      {/if}
    </button>
    <button on:click={handleClick}>
      test click
    </button>
    <h1>
      <div>Player 1 Score:{scoreO}</div>
      <div>Player 2 Score:{score1}</div>
      <!-- <div>
        X: {x}
      </div>
      <div>
        Y:{y}
      </div> -->
      <div>
        <Circle {handleClick}{cx}{cy}{size} display={gameRun ? 'block' : 'none'}/>
        <Cursor1 {p2x}{p2y}/>
      </div>
    </h1>
  </main>
  
  <style>
  
  </style>
  