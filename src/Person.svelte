<script>
  export let person;

  const dx = 6;
  const offset = 30 * dx;
</script>

{#if person.infected == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="red"
  />
{:else if person.vacc == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="blue"
  />
{:else if person.susceptible == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="#ccccff"
  />
{:else}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="#22cc22"
  />
{/if}
