<script>

    export let person
    export let dx

    // console.log(person)

</script>

{#if person.susceptible == 1}
    <circle cx={person.x * dx + 10} cy={person.y * dx + 10} r="5" stroke="black" stroke-width="1" fill="#ccffff" />    
{:else if person.infected == 1}
    <circle cx={person.x * dx + 10} cy={person.y * dx + 10} r="5" stroke="black" stroke-width="1" fill="red" />    
{:else}
    <circle cx={person.x * dx + 10} cy={person.y * dx + 10} r="5" stroke="black" stroke-width="1" fill="palegreen" />
{/if}
