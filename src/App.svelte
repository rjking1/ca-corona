<script>
  import Population from "./Population.svelte";
  //import AChart from "./AChart.svelte"

  let p;
  let n = 2000;
  let i = 1;
  let a = 10;
  let b = 6;
</script>

<div>
  Init pop size:
  <input type="number" bind:value={n} min="1" max="5000" />
  <input type="range" bind:value={n} min="1" max="5000" />

  Init infected:
  <input type="number" bind:value={i} min="1" max="500" />
  <input type="range" bind:value={i} min="1" max="500" />

  <button type="submit" on:click={() => p.init(n, 100, 100, i)}> Init </button>

  <button type="submit" on:click={() => p.tick()}> Step </button>

  <button type="submit" on:click={() => p.run()}> Run </button>

  <button type="submit" on:click={() => p.stop()}> Stop </button>

  Recover after days:
  <input type="number" bind:value={a} min="1" max="20" />
  <input type="range" bind:value={a} min="1" max="20" />

  Max Movement:
  <input type="number" bind:value={b} min="0" max="20" />
  <input type="range" bind:value={b} min="0" max="20" />
</div>

<Population bind:this={p} {n} recoverAfterDays={a} maxMove={b} />

<style>
</style>
