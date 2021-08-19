<script>
  import Chart from "svelte-frappe-charts";

  export let recoverAfterDays;
  export let maxMove;

  import Person from "./Person.svelte";

  let tot_pop = 0;
  let tot_inf = 0;
  let tot_rec = 0;
  let persons = new Map();
  let timerId;
  let data;
  let labels = [];
  let inf_hist = [];

  data = {
    labels: labels,
    datasets: [
      {
        values: inf_hist,
      },
    ],
  };

  const dx = 6;

  function hash(x, y) {
    return x * 10000 + y;
  }

  function move(person, dist) {
    const offset_x = (Math.round(Math.random() * 2.0) - 1.0) * dist;
    const offset_y = (Math.round(Math.random() * 2.0) - 1.0) * dist;
    if (!persons.has(hash(person.x + offset_x, person.y + offset_y))) {
      person.x += offset_x;
      person.y += offset_y;
    }
    return person;
  }

  function decreaseInfected(person) {
    if (person.infected == 1) {
      person.daysInfected += 1;
      if (person.daysInfected > recoverAfterDays) {
        person.infected = 0;
      }
    }
  }

  function countInfectedNeighbours(person) {
    const nbours = [
      { x: -1, y: 0 },
      { x: -1, y: -1 },
      { x: 0, y: -1 },
      { x: 1, y: -1 },
      { x: 1, y: 0 },
      { x: 1, y: 1 },
      { x: 0, y: 1 },
      { x: -1, y: 1 },
    ];

    if (person.infected) {
      return person; // don't keep 2 neighbours infecting each other
    }

    // if prev infected then immune
    if (!person.susceptible) {
      return person;
    }

    let inf = 0;
    nbours.forEach((offset) => {
      if (persons.has(hash(person.x + offset.x, person.y + offset.y))) {
        inf += persons.get(
          hash(person.x + offset.x, person.y + offset.y)
        ).infected;
      }
    });
    if (inf == 0) {
      return person;
    }

    let { ...person1 } = person;
    person1.infected = 1;
    person1.daysInfected = 0;
    person1.susceptible = 0;
    return person1;
  }

  function updateCounts() {
    tot_pop = 0;
    tot_inf = 0;
    tot_rec = 0;
    Array.from(persons.values()).forEach((person) => {
      tot_pop += 1;
      if (person.infected == 1) {
        tot_inf += 1;
      }
      if (person.susceptible == 0) {
        tot_rec += 1;
      }
    });
    inf_hist.push(tot_inf);
    labels.push(inf_hist.length); // label is # days

    data = data;
  }

  export function init(n, x, y, inf) {
    if (timerId) {
      clearInterval(timerId);
    }
    persons.clear();
    for (let i = 0; i < n; i++) {
      let person = {
        x: Math.floor(Math.random() * x),
        y: Math.floor(Math.random() * y),
        infected: inf-- > 0 ? 1 : 0,
        daysInfected: 0,
        susceptible: 1,
      };
      if (person.infected == 1) {
        person.daysInfected = 0; //Math.floor(Math.random() * recoverAfterDays) + 1;
        person.susceptible = 0;
      }
      if (!persons.has(hash(person.x, person.y))) {
        persons.set(hash(person.x, person.y), person);
      }
    }
    labels.length = 0;
    inf_hist.length = 0;
    updateCounts();
    persons = persons;
  }

  export function tick() {
    let persons1 = new Map();
    Array.from(persons.values()).forEach((person) => {
      let person1 = countInfectedNeighbours(person);
      if (Math.random() < 0.2) {
        move(person1, maxMove);
      }
      decreaseInfected(person1);
      persons1.set(hash(person1.x, person1.y), person1);
    });
    updateCounts();
    persons = persons1;
  }

  export function run() {
    stop();
    timerId = setInterval(() => {
      tick();
      if (tot_inf == 0) {
        clearInterval(timerId);
      }
    }, 500);
  }

  export function stop() {
    if (timerId) {
      clearInterval(timerId);
    }
  }
</script>

<div class="flex-container">
  <div class="flex-item">
    <div>Population: {tot_pop} Infected: {tot_inf} Recovered: {tot_rec}</div>
    <svg id="svg" height="1010px" width="1010px">
      {#each Array.from(persons.values()) as p}
        <Person person={p} {dx} />
      {/each}
    </svg>
  </div>
  <div class="flex-item">
    {#if true}
      <Chart {data} type="bar" />
    {/if}
  </div>
</div>

<style>
  .flex-container {
    display: flex;
    flex-flow: row wrap;

    /* Then we define how is distributed the remaining space */
    justify-content: space-around;

    padding: 0;
    margin: 0;
    xlist-style: none;
  }

  .flex-item {
    width: 600px;
    xheight: 150px;
  }
</style>
