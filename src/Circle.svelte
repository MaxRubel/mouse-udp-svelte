<script>
    export let handleClick;
    export let cx;
    export let cy;
    export let size
    export let display
    </script>
    
    <main>
        <button class="circle" style="height: {size}px; width: {size}px; left:{cx}px; top: {cy}px; display: {display};" on:click={handleClick}>
        </button>
    </main>
    
    <style>
        .circle{
            position: absolute;
            cursor: pointer;
            display: block;
            height: 150px;
            width: 150px;
            border-radius: 100%;
            background-color: aquamarine;
        }
    </style>