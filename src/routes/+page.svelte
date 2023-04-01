<script>
 import { onMount } from "svelte";

  let data;
  let i = 0;
  let jsonData = [];
  let guess = Number;
  let guessed = false;
  let score = 0; 

 
  onMount(() => {
    fetchData();
  });

  async function fetchData() {
    let response = await fetch("https://guesstheprice.net/api/ran");
    data = response.json()
    data.then(json => {
      jsonData = json
      showData(json, i)
    });
  }

  function showData(json, index) {
    if (index <= 12) {
    data = json[index];
    } else {
      fetchData();
      i = 0;
    }
  }

  function onClickedGuess(jsonData) {
    if (guess == undefined || guess == NaN) {
      guessed = false;
    } else {
      guessed = true;
      let percent = guess/(data.price / 100);
      console.log(percent);
      let points = 0;

      if (percent == 100) {
        points = 10;
      } else if (percent >= 90 && percent <= 110) {
        points = 5;
      } else if (percent >= 85 && percent <= 115) {
        points = 4;
      } else if (percent >= 80 && percent <= 120) {
        points = 3
      } else if (percent >= 75 && percent <= 125) {
        points = 2;
      } else if (percent >= 70 && percent <= 130) {
        points = 1
      } else {return}
      score = score + points;
    }
  }

  function onClickedNext() {
    i = i + 1;
    showData(jsonData, i);
    guessed = false;
    guess = undefined;
  }

</script>



{#await fetchData()}
  <div class="AroundContent">Fetching resource</div>
{:then}
  <h1 id="title">Guess the price</h1>
  {#if guessed}
      <h2>{data.name}</h2>
      <h4>{data.description}</h4>
      <img src={data.imageUrl} alt={data.imageUrl} class="guessPicture" style="max-height: 300px; max-width: 500px">
      <h4>Your guess: {guess}</h4>
      <h4>Actual price: {data.price}</h4>
    <button class="click" on:click={() => onClickedNext()}>Next</button>
  {:else}
      <h2>{data.name}</h2>
      <h4>{data.description}</h4>
      <img src={data.imageUrl} alt={data.imageUrl} class="guessPicture" style="max-height: 300px; max-width: 500px">
      <input type="number" bind:value={guess}>    
      <button on:click={() => {onClickedGuess(jsonData)}}>Guess</button>
      <div id="pointsDiv">
        <h3>Points</h3>
        <h4>{score}</h4>
    </div>
    <button class="click" on:click={() => onClickedNext()} >Next</button>
  {/if}
{/await}



