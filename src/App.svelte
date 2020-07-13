<script>
  import { onMount } from 'svelte';

  // api url
  const apiBaseUrl = 'https://opentdb.com/api.php?amount=1&type=multiple';

  // set variables
  let trivias = [];
  let trivia = '';

  let question = '';
  let category = '';
  let difficulty = '';
  let correctAnswer = '';
  let shuffledAnswers = [];

  let selectedAnswer = '';

  // fetch trivia
  onMount(async () => {
    const res = await fetch(apiBaseUrl);
    trivias = await res.json();
    trivia = trivias.results[0];

    // get trivia question
    question = htmlSanitize(trivia.question);

    // get trivia category
    category = htmlSanitize(trivia.category);

    // get difficulty
    difficulty = htmlSanitize(trivia.difficulty);

    // get correct answer
    correctAnswer = htmlSanitize(trivia.correct_answer);

    // get incorrect answers array
    let incorrectAnswersArr = trivia.incorrect_answers;

    // combine all answers
    let combinedAnswersArr = incorrectAnswersArr;
    combinedAnswersArr.push(correctAnswer);

    // shuffle all answers
    shuffledAnswers = shuffle(combinedAnswersArr);

    console.log('ANS:', correctAnswer);
  });

  // Shuffle Answers in array - from stackoverflow
  function shuffle(array) {
    let currentIndex = array.length,
      temporaryValue,
      randomIndex;

    // While there remain elements to shuffle...
    while (0 !== currentIndex) {
      // Pick a remaining element...
      randomIndex = Math.floor(Math.random() * currentIndex);
      currentIndex -= 1;

      // And swap it with the current element.
      temporaryValue = array[currentIndex];
      array[currentIndex] = array[randomIndex];
      array[randomIndex] = temporaryValue;
    }

    return array;
  }

  // convert html entities to symbols
  function htmlSanitize(str) {
    return String(str)
      .replace(/&amp;/g, '&')
      .replace(/&lt;/g, '<')
      .replace(/&gt;/g, '>')
      .replace(/&quot;/g, '"')
      .replace(/&#039;/g, "'")
      .replace(/&eacute;/g, 'é');
  }

  function handleClick(e) {
    selectedAnswer = e.target.value;
  }

  function handleNext() {
    // reload page
  }
</script>

<main>
  {#if trivia.length === 0}
    <h1>Loading...</h1>
  {:else}
    <!-- display trivia info -->
    <h2>{category}</h2>
    <h3>{difficulty === 'medium' ? 'MODERATE' : difficulty.toUpperCase()}</h3>
    <h1>{question}</h1>
    <hr />
    {#each shuffledAnswers as answer}
      <button class="box" on:click={handleClick} value={htmlSanitize(answer)}>
        {htmlSanitize(answer)}
      </button>
    {/each}
    {#if selectedAnswer === correctAnswer}
      <div class="msg correct">{correctAnswer} is Correct!</div>
    {/if}
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  h1 {
    font-size: 2em;
    font-weight: 300;
  }

  h2 {
    font-weight: 600;
  }

  h3 {
    font-weight: 400;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }

  .box {
    display: inline-block;
    border: 2px solid #333;
    border-radius: 5px;
    margin: 1rem;
    padding: 1rem;
    transition: 250ms ease-in-out;
    cursor: pointer;
  }

  .box:hover {
    background-color: coral;
  }

  .msg {
    font-size: 2rem;
  }

  .correct {
    color: green;
  }
</style>
