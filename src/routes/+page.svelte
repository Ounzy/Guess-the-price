<script>
 import { onMount } from "svelte";

  let data;
  let i = 0;
  let jsonData = [];
  let guess = Number;
  let guessed = false;

 
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
    console.log(data);
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
    <div class="mid">
      <h2>{data.name}</h2>
      <img src={data.imageUrl} alt={data.imageUrl} class="guessPicture" style="max-height: 300px; max-width: 500px">
      <h4>Your guess: {guess}</h4>
      <h4>Actual price: {data.price}</h4>
    </div>
    <button class="click" on:click={() => onClickedNext()}>Next</button>
  {:else}
    <div class="mid">
      <h2>{data.name}</h2>
      <img src={data.imageUrl} alt={data.imageUrl} class="guessPicture" style="max-height: 300px; max-width: 500px">
      <input type="number" bind:value={guess}>
      <button on:click={() => {onClickedGuess(jsonData)}}>Guess</button>
    </div>
    <button class="click" on:click={() => onClickedNext()} >Next</button>
  {/if}
{/await}



