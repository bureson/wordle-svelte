<script>
    export let className;
    export let letter;

    $: upperLetter = letter.toUpperCase();
</script>

<div class="letter {className}">
    {upperLetter}
</div>

<style>
    .letter {
        vertical-align: top;
        display: inline-block;
        border: 1px solid #333;
        font-size: 20px;
        color: #fff;
        height: 60px;
        line-height: 60px;
        width: 60px;
        margin: 0 5px 10px;
    }
    .letter.correct {
        background-color: #477443;
    }
    .letter.misplaced {
        background-color: #c0a83d;
    }
    .letter.wrong {
        background-color: #333;
    }
</style>