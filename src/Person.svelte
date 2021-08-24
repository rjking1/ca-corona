<script>
  export let person;

  const dx = 3.5;
  const rad = 1.5;
  const offset = 20 * dx;
</script>

{#if person.infected == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r={rad}
    stroke="none"
    fill="red"
  />
{:else if person.vacc == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r={rad}
    stroke="none"
    fill="blue"
  />
{:else if person.susceptible == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r={rad}
    stroke="none"
    fill="#ccccff"
  />
{:else}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r={rad}
    stroke="none"
    fill="#22cc22"
  />
{/if}
