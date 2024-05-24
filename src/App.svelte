<script>
  // @ts-nocheck

  import {onDestroy, onMount } from "svelte";
  import ChoosePlayer from "./ChoosePlayer.svelte";
  import Game from "./Game.svelte";



  let scoreO = 0;
  let score1 = 0;

  let playersChosen = false;

  let player1 = false;
  let player2 = false;

  const ws = new WebSocket("ws://localhost:8080/ws");

  let youArePlayer = 0

  onMount(() => {
    ws.onopen = () => {
      console.log("Connected to server");
    };
    
  });

const choosePlayerNo = (no) => {
  console.log("clicked itttt:", no)
  
  // if(youArePlayer !== 0){
  //   return
  // }

  let buffer = new ArrayBuffer(2);
  let view = new DataView(buffer);

  if(no === 1){
    view.setUint8(0, 2, true);
    view.setUint8(1, 1, true);
  }

  if(no === 2){
    view.setUint8(0, 2, true);
    view.setUint8(1, 2, true);
  }  
    ws.send(buffer);
    console.log(buffer)
}


ws.onmessage = (event) => {
if(event.data[0]==2){
  if(event.data[1]==1){
    player1 = true;
    youArePlayer = 1;
  }
  if(event.data[1]==2){
    player2 = true
    youArePlayer = 2
  }
}
};

onDestroy(()=>{
  ws.close()
})

$: {if(player1 && player2){
  playersChosen = true
}}

</script>

<main>
  {#if !playersChosen}
  <ChoosePlayer {choosePlayerNo}{player1}{player2}/>
  {:else} 
  <Game {youArePlayer}/>
  {/if}
  </main>
  

<style>

</style>
