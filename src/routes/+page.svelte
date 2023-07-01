<script>
    import * as R from 'ramda';
    import Letter from '../component/letter.svelte';

    let rightWordList = ['jarda', 'ondra', 'lubos', 'milan', 'petra', 'libor', 'honza', 'jitka', 'hanka', 'irena', 'josef', 'radim', 'cyril', 'vojta', 'tomas', 'alice', 'robin', 'hynek', 'lenka', 'matej', 'mirek', 'radek', 'ivana', 'zofie', 'aneta', 'filip', 'adolf', 'alois', 'pavel', 'karel', 'jakub', 'oskar', 'klara', 'alena', 'ludek', 'marie'];
    let rightWord = rightWordList[Math.floor(Math.random() * rightWordList.length)];
    let wordList = [''];

    let round = 0;
    let success = false;
    let indexList = Array.from(Array(5).keys());
    let roundList = Array.from(Array(6).keys());

    const onKeyDown = event => {
        const word = wordList[round];
        if (success) return;
        if (event.altKey || event.ctrlKey || event.shiftKey) return;
        if (event.keyCode >= 65 && event.keyCode <= 90 && word.length < 5) { // Note: letters a-z
            event.preventDefault();
            wordList[round] = word + event.key;
        } else if (event.keyCode === 8) { // Note: backspace
            event.preventDefault();
            wordList[round] = word.substring(0, word.length - 1);
        } else if (event.keyCode === 13 && word.length === 5) { // Note: enter
            if (word === rightWord) {
                success = true;
            } else {
                event.preventDefault();
                ++round;
                wordList.push('');
            }
        }
    };

    const letterGetter = (wordList, roundIndex, letterIndex) => {
        const word = wordList[roundIndex] || '';
        const letter = word[letterIndex] || '';
        return letter;
    };

    const classGetter = (wordList, roundIndex, letterIndex, currentRound, success) => {
        if (success ? roundIndex > currentRound : roundIndex >= currentRound) return 'early';
        console.log({ wordList, roundIndex, word: wordList[roundIndex] });
        const wordLetterList = wordList[roundIndex].split('');
        const letter = wordLetterList[letterIndex];
        if (rightWord[letterIndex] === letter) return 'correct';
        if (rightWord.includes(letter)) {
            const fixedRightWordLetterList = rightWord.split('').map((letter, index) => wordLetterList[index] === letter ? null : letter);
            const letterCount = fixedRightWordLetterList.reduce((count, thisLetter) => letter === thisLetter ? count + 1 : count, 0);
            const thisWordLetterCount = R.slice(0, letterIndex, wordLetterList).reduce((count, thisLetter) => letter === thisLetter ? count + 1 : count, 0);
            if (fixedRightWordLetterList.includes(letter) && thisWordLetterCount <= letterCount) return 'misplaced';
        }
        return 'wrong';
    };
</script>

<div class="container">
    <h1>Wordle in Svelte</h1>
    <p>A recreation of the famous game Wordle made in the front-end Javascript framework Svelte.</p>
    <p>Guess Czech names without interpunction.</p>
    <div class="game">
        {#each roundList as roundIndex}
            <div class="round-row">
                {#each indexList as letterIndex}
                    <Letter
                        className={classGetter(wordList, roundIndex, letterIndex, round, success)}
                        letter={letterGetter(wordList, roundIndex, letterIndex)}
                    />
                {/each}
            </div>
        {/each}
    </div>
    {#if success}
        <p class="success">You guessed it!</p>
    {/if}
    <p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
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
    .success {
        color: red;
        font-size: 24px;
    }
</style>
