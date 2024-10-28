<script>
  let level = $state(4);
  let n = $derived(Math.ceil(Math.sqrt(level * 4)));

  let tiles = $derived(Array.from({ length: n * n }, (_, i) => i));

  let toggled = $state(Array(n * n).fill(false));
  let solution = $state(Array(n * n).fill(false));

  let showing_solution = $state(false);
  let can_toggle_tiles = false;

  let round_in_progress = $state(false);

  let accuracy = $state(0);

  /**
   * Delays for a given number of milliseconds
   * @param {number} ms
   */
  function delay(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }

  function randomTile() {
    return Math.floor(Math.random() * (n * n));
  }

  function calculateScore() {
    return (
      toggled
        .map((v, i) => v == solution[i])
        .reduce((p, c) => (c ? p + 1 : p), 0) / toggled.length
    );
  }

  async function submit() {
    if (!round_in_progress) {
      return;
    }

    showing_solution = true;
    can_toggle_tiles = false;
    accuracy = calculateScore();

    await delay(Math.log(level) * 1000);

    if (accuracy > 0.9) {
      level = level + 1;
    } else {
      level = level - 1;
    }

    showing_solution = false;
    round_in_progress = false;

    toggled = Array(n * n).fill(false);
    solution = Array(n * n).fill(false);
  }

  async function newRound() {
    round_in_progress = true;
    can_toggle_tiles = false;
    showing_solution = true;

    generateSolution();
    await delay((level / 2) * 1000);

    showing_solution = false;
    can_toggle_tiles = true;
  }

  function generateSolution() {
    let new_solution = Array(n * n).fill(false);
    for (let i = 0; i < level; i++) {
      let chosenTile = randomTile();
      while (new_solution[chosenTile] == true) {
        chosenTile = randomTile();
      }
      new_solution[chosenTile] = true;
    }
    solution = new_solution;
  }

  /**
   * Toggles the index of a tile
   * @param {number} index
   */
  function toggle(index) {
    toggled[index] = !toggled[index];
  }
</script>

<div class="body">
  <h1>Memory Game</h1>
  <p>Level: {level}</p>

  <div class="grid-container">
    <div class="grid" style="--grid-size: {n}">
      {#each tiles as tile}
        <button
          class="tile"
          class:active={toggled[tile]}
          class:highlighted={showing_solution && solution[tile]}
          class:incorrect={showing_solution && !solution[tile] && toggled[tile]}
          class:correct={showing_solution && solution[tile] && toggled[tile]}
          onclick={() => {
            if (can_toggle_tiles) {
              toggle(tile);
            }
          }}
          aria-label="Test"
        ></button>
      {/each}
    </div>

    <div class="button-container">
      <button onclick={newRound} disabled={round_in_progress}
        >Start Round</button
      >
      <button onclick={submit} disabled={!round_in_progress}
        >Check Answer</button
      >
    </div>
  </div>
</div>

<style>
  :global(body) {
    background-color: #cbc3e3;
    height: 100vh;
    display: flex;
  }

  :global(p, button, h1) {
    font-family: "carving-soft";
  }

  .tile {
    border: 1px solid black;
    background-color: white;
  }

  .highlighted {
    background-color: cadetblue;
  }

  .active {
    background-color: aquamarine;
  }

  .correct {
    background-color: rgb(0, 165, 55);
  }

  .incorrect {
    background-color: lightcoral;
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(var(--grid-size), 1fr);
    grid-template-rows: repeat(var(--grid-size), 1fr);
    aspect-ratio: 1/1;
    width: 50%;
  }

  .body {
    background-color: white;
    border-radius: 2ch;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 50%;
    margin: auto;
    padding: 20px;
  }

  .button-container {
    display: flex;
    justify-content: space-around;
    width: 100%;
    margin-top: 20px;
  }

  button {
    background-color: white;
    transition: background-color 0.05s ease-out;
    border: 1px solid black;
    border-radius: 5px;
    padding: 10px;
  }

  .grid-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  @font-face {
    font-family: "carving-soft";
    font-style: normal;
    font-weight: 500;
    src: url("/fonts/Soft.otf");
  }
</style>
