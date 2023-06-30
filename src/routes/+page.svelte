<script>
    let word = '';
    let indexList = Array.from(Array(5).keys())
    const onKeyDown = event => {
        if (event.keyCode >= 65 && event.keyCode <= 90 && word.length < 5) { // Note: letters a-z
            event.preventDefault();
            word += event.key;
        } else if (event.keyCode === 8) { // Note: backspace
            event.preventDefault();
            word = word.substring(0, word.length - 1);
        } else if (event.keyCode === 13) { // Note: enter
            event.preventDefault();
            console.log('word is:', word);
        }
    };
</script>

<div class="container">
    <h1>Wordle built with Svelte</h1>
    <p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
    {#each indexList as index}
        <div class="letter">{(word[index] || '').toUpperCase()}</div>
    {/each}
</div>

<svelte:window on:keydown={onKeyDown} />

<style>
    h1 {
        color: aqua;
    }
    .container {
        width: 50%;
        margin: 100px auto;
        text-align: center;
        vertical-align: top;
    }
    .letter {
        vertical-align: top;
        display: inline-block;
        border: 1px solid #fff;
        font-size: 20px;
        color: #fff;
        height: 80px;
        line-height: 80px;
        width: 60px;
        margin: 0 5px;
    }
</style>
