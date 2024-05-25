<script>

export let startGame

let count = 5;
let interval;

interval = setInterval(()=>{
    count--
}, 1000)

$:{if (count === 0){
    clearInterval(interval);
    startGame();
}}

</script>

<main>
    <div>
        Starting in: {count}
    </div>
</main>