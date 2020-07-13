<script>
  import { onMount } from 'svelte';

  // api url
  const apiBaseUrl = 'https://opentdb.com/api.php?amount=1&type=multiple';

  // set trivias array
  let trivias = [];
  let trivia = '';

  // fetch trivia
  onMount(async () => {
    const res = await fetch(apiBaseUrl);
    trivias = await res.json();
    trivia = trivias.results[0];
    console.log(trivia);
  });

  // convert html entities to symbols
  function htmlEntities(str) {
    return String(str)
      .replace(/&amp;/g, '&')
      .replace(/&lt;/g, '<')
      .replace(/&gt;/g, '>')
      .replace(/&quot;/g, '"')
      .replace(/&#039;/g, "'");
  }

  function handleNext() {
    // reload page
  }
</script>

<main>
  {#if trivia.length === 0}
    <h1>Loading...</h1>
  {:else}
    <h2>{trivia.category.toUpperCase()}</h2>
    <h3>
      DIFFICULTY: {trivia.difficulty.toUpperCase() === 'MEDIUM' ? 'MODERATE' : trivia.difficulty.toUpperCase()}
    </h3>
    <h1>{htmlEntities(trivia.question)}</h1>

    <p>
      <strong>A.</strong>
      {htmlEntities(trivia.incorrect_answers[0])}
    </p>
    <p>
      <strong>B.</strong>
      {htmlEntities(trivia.incorrect_answers[1])}
    </p>
    <p>
      <strong>C.</strong>
      {htmlEntities(trivia.incorrect_answers[2])}
    </p>
    <p>
      <strong>D.</strong>
      {htmlEntities(trivia.correct_answer)}
    </p>
    <br />
    <br />
    <br />
    <br />
    <button on:click={handleNext}>Next Question</button>
  {/if}

</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    font-size: 2em;
    font-weight: 200;
  }

  h3 {
    font-weight: 400;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
