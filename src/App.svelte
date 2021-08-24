<script>
  import Population from "./Population.svelte";

  let p;
  let n = 7000;
  let i = 1; // init infected
  let a = 7; // recover after days = days infectious
  let b = 5; // max move
  let r = 3;
  let vr = 0; // vacc # / day
  let seed = "42";
</script>

<div>
  Initial pop size:
  <input id="pop" type="number" bind:value={n} min="1" max="10000" />
  <!-- <input type="range" bind:value={n} min="1" max="10000" /> -->

  Initial infected:
  <input id="init" type="number" bind:value={i} min="1" max="1000" />
  <!-- <input type="range" bind:value={i} min="1" max="500" /> -->

  Infect neighbours:
  <input id="nbors" type="number" bind:value={r} min="1" max="8" />
  <!-- <input type="range" bind:value={r} min="0" max="6" /> -->

  <button type="submit" on:click={() => p.init(n, 200, 200, i)}> Init </button>

  <button type="submit" on:click={() => p.tick()}> Step </button>

  <button type="submit" on:click={() => p.run()}> Run </button>

  <button type="submit" on:click={() => p.stop()}> Stop </button>

  <br />
  Days infectious:
  <input id="days" type="number" bind:value={a} min="1" max="15" />
  <!-- <input type="range" bind:value={a} min="1" max="20" /> -->

  Max Movement:
  <input id="maxmove" type="number" bind:value={b} min="1" max="10" />
  <!-- <input type="range" bind:value={b} min="0" max="20" /> -->

  Vacc rate (#/day):
  <input id="vacc" type="number" bind:value={vr} min="0" max="100" />
  <!-- <input type="range" bind:value={vr} min="0" max="1" /> -->

  Seed:
  <input id="seed" type="text" bind:value={seed} min="0" max="999" />
</div>

<Population
  bind:this={p}
  recoverAfterDays={a}
  maxMove={b}
  vaccPerDay={vr}
  {r}
  {seed}
/>

<style>
</style>
