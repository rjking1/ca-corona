<script>
  import Population from "./Population.svelte";
  //import AChart from "./AChart.svelte"

  let p;
  let n = 2000;
  let i = 1; // init infected
  let a = 10; // recover after days
  let b = 5; // max move
  let r = 3;
  let vr = 0; // vacc # / day
  let age = 70; // age for vacc
</script>

<div>
  Initial pop size:
  <input type="number" bind:value={n} min="1" max="5000" />
  <!-- <input type="range" bind:value={n} min="1" max="5000" /> -->

  Initial infected:
  <input type="number" bind:value={i} min="1" max="500" />
  <!-- <input type="range" bind:value={i} min="1" max="500" /> -->

  R:
  <input type="number" bind:value={r} min="1" max="6" />
  <!-- <input type="range" bind:value={r} min="0" max="6" /> -->

  <button type="submit" on:click={() => p.init(n, 100, 100, i)}> Init </button>

  <!-- <button type="submit" on:click={() => p.tick()}> Step </button> -->

  <button type="submit" on:click={() => p.run()}> Run </button>

  <button type="submit" on:click={() => p.stop()}> Stop </button>

  <br />
  Days infectious:
  <input type="number" bind:value={a} min="1" max="20" />
  <!-- <input type="range" bind:value={a} min="1" max="20" /> -->

  Max Movement:
  <input type="number" bind:value={b} min="1" max="10" />
  <!-- <input type="range" bind:value={b} min="0" max="20" /> -->

  Vacc rate (#/day):
  <input type="number" bind:value={vr} min="0" max="100" />
  <!-- <input type="range" bind:value={vr} min="0" max="1" /> -->

  Vacc over age:
  <input type="number" bind:value={age} min="0" max="100" />
  <!-- <input type="range" bind:value={vr} min="0" max="1" /> -->
</div>

<Population
  bind:this={p}
  recoverAfterDays={a}
  maxMove={b}
  vaccPerDay={vr}
  {r}
  {age}
/>

<style>
</style>
