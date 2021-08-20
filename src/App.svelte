<script>
  import Population from "./Population.svelte";
  //import AChart from "./AChart.svelte"

  let p;
  let n = 2000;
  let i = 10;
  let a = 10; // recover after days
  let b = 0; // max move
  let r = 3;
  let vr = 1; // vacc # / day
</script>

<div>
  Initial pop size:
  <input type="number" bind:value={n} min="1" max="5000" />
  <!-- <input type="range" bind:value={n} min="1" max="5000" /> -->

  Initial infected:
  <input type="number" bind:value={i} min="1" max="500" />
  <!-- <input type="range" bind:value={i} min="1" max="500" /> -->

  R:
  <input type="number" bind:value={r} min="0" max="6" />
  <!-- <input type="range" bind:value={r} min="0" max="6" /> -->

  <button type="submit" on:click={() => p.init(n, 100, 100, i)}> Init </button>

  <button type="submit" on:click={() => p.tick()}> Step </button>

  <button type="submit" on:click={() => p.run()}> Run </button>

  <button type="submit" on:click={() => p.stop()}> Stop </button>

  <br />
  Days infectious:
  <input type="number" bind:value={a} min="1" max="20" />
  <!-- <input type="range" bind:value={a} min="1" max="20" /> -->

  Max Movement:
  <input type="number" bind:value={b} min="0" max="20" />
  <!-- <input type="range" bind:value={b} min="0" max="20" /> -->

  Vacc rate (#/day):
  <input type="number" bind:value={vr} min="0" max="100" />
  <!-- <input type="range" bind:value={vr} min="0" max="1" /> -->
</div>

<Population
  bind:this={p}
  {n}
  recoverAfterDays={a}
  maxMove={b}
  vaccPerDay={vr}
  {r}
/>

<style>
</style>
