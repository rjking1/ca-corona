<script>
  export let person;
  export let dx;

  const offset = 30 * dx;

  // console.log(person)
</script>

{#if person.susceptible == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="#ccccff"
  />
{:else if person.infected == 1}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="red"
  />
{:else}
  <circle
    cx={person.x * dx + offset}
    cy={person.y * dx + offset}
    r="2"
    stroke="none"
    fill="green"
  />
{/if}
