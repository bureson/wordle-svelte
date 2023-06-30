<script>
    let rightWord = 'jarda';
    let wordList = [''];

    let round = 0;
    let indexList = Array.from(Array(5).keys());
    let roundList = Array.from(Array(6).keys());

    const onKeyDown = event => {
        const word = wordList[round];
        if (event.altKey || event.ctrlKey || event.shiftKey) return;
        if (event.keyCode >= 65 && event.keyCode <= 90 && word.length < 5) { // Note: letters a-z
            event.preventDefault();
            wordList[round] = word + event.key;
        } else if (event.keyCode === 8) { // Note: backspace
            event.preventDefault();
            wordList[round] = word.substring(0, word.length - 1);
        } else if (event.keyCode === 13 && word.length === 5) { // Note: enter
            event.preventDefault();
            console.log('word is:', word);
            ++round;
            wordList.push('');
        }
        console.log(wordList);
    };

    const letterGetter = (wordList, roundIndex, letterIndex) => {
        const word = wordList[roundIndex] || '';
        const letter = word[letterIndex] || '';
        return letter;
    };

    const upperLetter = (wordList, roundIndex, letterIndex) => {
        return letterGetter(wordList, roundIndex, letterIndex).toUpperCase();
    }

    const classGetter = (wordList, roundIndex, letterIndex, currentRound) => {
        if (roundIndex >= currentRound) return 'early';
        const letter = letterGetter(wordList, roundIndex, letterIndex);
        console.log({ rightWord, letter, inc: rightWord.includes(letter) });
        return rightWord[letterIndex] === letter
            ? 'correct'
            : rightWord.includes(letter)
                ? 'misplaced'
                : 'wrong';
    };
</script>

<div class="container">
    <h1>Wordle built with Svelte</h1>
    <p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
    <div class="game">
        {#each roundList as roundIndex}
            <div class="round-row">
                {#each indexList as letterIndex}
                    <div class="letter {classGetter(wordList, roundIndex, letterIndex, round)}">{upperLetter(wordList, roundIndex, letterIndex)}</div>
                {/each}
            </div>
        {/each}
    </div>
</div>

<svelte:window on:keydown={onKeyDown} />

<svelte:head>
	<title>Wordle</title>
</svelte:head>

<style>
    h1 {
        color: aqua;
    }
    a {
        color: burlywood;
    }
    .container {
        width: 50%;
        margin: 100px auto;
        text-align: center;
        vertical-align: top;
    }
    .game {
        margin: 60px 0 0;
    }
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
