
<script>
    export let choosePlayerNo;
    export let player1;
    export let player2;

</script>

<main>
    <button on:click={()=>{
        choosePlayerNo(1)
    }}
    disabled={player1}
    >
        Player 1
    </button>

    <button on:click={()=>{
        choosePlayerNo(2)
    }}
    disabled={player2}
    >
        Player 2
    </button>
</main>
