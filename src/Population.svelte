<script>
  import { chart } from "svelte-apexcharts";

  export let recoverAfterDays;
  export let maxMove;
  export let vaccPerDay;
  export let r;
  export let age;

  import Person from "./Person.svelte";

  let tot_pop = 0;
  let tot_inf = 0;
  let tot_rec = 0;
  let tot_vacc = 0;
  let rEff = 1;
  let persons = new Map();
  let timerId;
  let labels = [];
  let inf_hist = [];
  let inf_growth = [];

  $: chart_options = {
    chart: {
      animations: {
        enabled: false,
      },
      export: {
        csv: {
          filename: "ca-corona.csv",
          headerCategory: "day",
        },
      },
    },
    colors: ["#247BA0", "#FF1654"],
    //dataLabels: { position: "top", enabled: false, offsetY: 0 },
    series: [
      {
        name: "infected",
        type: "column",
        data: inf_hist,
      },
      {
        name: "R eff",
        type: "line",
        data: inf_growth,
      },
    ],
    xaxis: {
      categories: labels,
    },
    yaxis: [
      {
        decimalsInFloat: 0,
        title: {
          text: "infected",
        },
      },
      {
        opposite: true,
        decimalsInFloat: 2,
        title: {
          text: "R eff",
        },
      },
    ],
  };

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

  function updateCounts() {
    tot_pop = 0;
    let prev_inf = tot_inf;
    tot_inf = 0;
    tot_rec = 0;
    tot_vacc = 0;
    Array.from(persons.values()).forEach((person) => {
      tot_pop++;
      if (person.infected == 1) {
        tot_inf++;
      }
      if (person.susceptible == 0) {
        tot_rec++;
      }
      if (person.vacc == 1) {
        tot_vacc++;
      }
    });
    inf_hist.push(tot_inf);
    rEff = prev_inf != 0 ? tot_inf / prev_inf : 1;
    inf_growth.push(rEff);
    labels.push(inf_hist.length); // label is # days

    chart_options = chart_options;
  }

  export function init(n, x, y, inf) {
    if (timerId) {
      clearInterval(timerId);
    }
    persons.clear();
    let i = 0;
    while (i < n) {
      const xp = Math.random() * x;
      const yp = Math.random() * y;
      const z = (Math.sin(xp / 20 - 20) - Math.cos((yp / 10) * 1.3)) / 2.0; // -1.0 to 1.0
      if (z > Math.random() - 0.5) {
        let person = {
          x: Math.floor(xp),
          y: Math.floor(yp),
          susceptible: inf > 0 ? 0 : 1,
          infected: inf-- > 0 ? 1 : 0,
          daysInfected: 0,
          vacc: 0,
          age: Math.floor(Math.random() * 80),
          exposed: 0,
        };
        if (!persons.has(hash(person.x, person.y))) {
          persons.set(hash(person.x, person.y), person);
          i++;
        }
      }
    }
    labels.length = 0;
    inf_hist.length = 0;
    inf_growth.length = 0;
    updateCounts();
    persons = persons;
  }

  function infectNeighbours(person, r) {
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
    let rCount = 0;
    nbours.forEach((offset) => {
      if (persons.has(hash(person.x + offset.x, person.y + offset.y))) {
        if (++rCount <= r) {
          persons.get(hash(person.x + offset.x, person.y + offset.y)).exposed++;
        }
      }
    });
  }

  export function tick() {
    Array.from(persons.values()).forEach((person) => {
      person.exposed = 0;
    });
    Array.from(persons.values()).forEach((person) => {
      if (person.infected == 1) {
        infectNeighbours(person, r);
      }
    });

    let new_persons = new Map();
    let v = 0;
    Array.from(persons.values()).forEach((person) => {
      let person1 = { ...person }; // copy
      if (person.infected == 1 && ++person1.daysInfected > recoverAfterDays) {
        // nasty -- must inc person1.daysInfected
        person1.infected = 0;
        person1.susceptible = 0; // ie recovered
      }
      if (
        person.susceptible == 1 &&
        person.infected == 0 &&
        person.exposed > 0 &&
        person.vacc == 0
      ) {
        person1.infected = 1;
        person1.daysInfected = 0;
      }
      if (person.vacc == 0 && person.age > age && ++v <= vaccPerDay) {
        person1.vacc = 1;
      }
      if (maxMove > 0 && Math.random() < 0.5) {
        move(person1, maxMove);
      }
      new_persons.set(hash(person1.x, person1.y), person1);
    });

    persons = new_persons;
    updateCounts();
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
    <div>
      <span style="color:red">Infected:</span>
      {tot_inf}
      <span style="color:#22cc22">Recovered:</span>
      {tot_rec}
      <span style="color:blue">Vaccinated:</span>
      {tot_vacc}
      <span style="color:red"
        >R eff:
        {rEff.toFixed(2)}</span
      >
    </div>
    <svg id="svg" height="1010px" width="1010px">
      {#each Array.from(persons.values()) as p}
        <Person person={p} />
      {/each}
    </svg>
  </div>
  <div class="flex-item">
    <div use:chart={chart_options} />
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
  }

  .flex-item {
    width: 600px;
  }
</style>
