<script>
    import Letter from '../component/letter.svelte';

    let inputEl;

    let rightWordList = ['jarda', 'ondra', 'lubos', 'milan', 'petra', 'libor', 'honza', 'jitka', 'hanka', 'irena', 'josef', 'radim', 'cyril', 'vojta', 'tomas', 'alice', 'robin', 'hynek', 'lenka', 'matej', 'mirek', 'radek', 'ivana', 'zofie', 'aneta', 'filip', 'adolf', 'alois', 'pavel', 'karel', 'jakub', 'oskar', 'klara', 'alena', 'ludek', 'marie'];
    let rightWord = rightWordList[Math.floor(Math.random() * rightWordList.length)];
    let wordList = [''];

    let round = 0;
    let success = false;
    let failed = false;
    let indexList = Array.from(Array(5).keys());
    let roundList = Array.from(Array(6).keys());

    const focusInput = () => inputEl?.focus();

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
            } if ((round + 1) >= roundList.length) {
                failed = true;
            } else {
                event.preventDefault();
                ++round;
                wordList.push('');
            }
        }
    };

    const resetGame = () => {
        round = 0;
        success = false;
        failed = false;
        wordList = [''];
        rightWord = rightWordList[Math.floor(Math.random() * rightWordList.length)];
    };

    const capitalize = (text) => {
        return text.replace(/^./, (str) => str.toUpperCase());
    };

    const letterGetter = (wordList, roundIndex, letterIndex) => {
        const word = wordList[roundIndex] || '';
        const letter = word[letterIndex] || '';
        return letter;
    };

    const evaluateWord = (guess, rightWord) => {
        const result = Array(guess.length).fill('wrong');
        const rightLetters = rightWord.split('');

        for (let i = 0; i < guess.length; i++) {
            if (guess[i] === rightLetters[i]) {
                result[i] = 'correct';
                rightLetters[i] = null; // Note: consume letter
            }
        }

        for (let i = 0; i < guess.length; i++) {
            if (result[i] === 'correct') continue;

            const index = rightLetters.indexOf(guess[i]);
            if (index !== -1) {
                result[i] = 'misplaced';
                rightLetters[index] = null; // Note: consume existence
            }
        }

        return result;
    };

    const classGetter = (wordList, roundIndex, letterIndex, currentRound, success) => {
        if (success ? roundIndex > currentRound : roundIndex >= currentRound) return 'early';

        const guess = wordList[roundIndex];
        const evaluation = evaluateWord(guess, rightWord);

        return evaluation[letterIndex];
    };
</script>

<div class="container">
    <h1>Wordle in Svelte</h1>
    <p>A recreation of the famous game Wordle made in the front-end Javascript framework Svelte.</p>
    <p>Guess Czech names without interpunction.</p>

    <input
        type="text"
        class="hidden-input"
        autocomplete="off"
        autocorrect="off"
        autocapitalize="none"
        spellcheck="false"
        bind:this={inputEl}
        on:keydown={onKeyDown}
    />
    
    {#if success}
        <p class="success">You guessed it! 🥳</p>
    {/if}
    {#if failed}
        <p class="failed">Bad luck! 😵 You were looking for {capitalize(rightWord)}.</p>
        <p class="failed">Better luck next time 🍀</p>
    {/if}
    {#if success || failed}
        <button on:click={resetGame}>Try again</button>
    {/if}

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
    <p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
</div>

<svelte:document on:click={focusInput} />

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
    button {
        padding: 10px 15px;
        border-radius: 10px;
        cursor: pointer;
        font-size: 18px;
    }
    .container {
        width: 100%;
        margin: 20px auto;
        text-align: center;
        vertical-align: top;
    }
    .round-row {
        white-space: nowrap;
    }
    .game {
        margin: 60px 0 0;
    }
    .hidden-input {
        position: absolute;
        opacity: 0;
        pointer-events: none;
    }
    .success, .failed {
        color: red;
        font-size: 24px;
    }
    @media (min-width: 600px) {
        .container {
            width: 50%;
            margin: 100px auto;
        }
    }
</style>
