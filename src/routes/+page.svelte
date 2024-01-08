<script>
  import { writable } from 'svelte/store';
  import { onMount } from 'svelte';
  import "../app.css";

  let birth;
  let interval;
  let age = writable(0);

  onMount(() => {
    let b = localStorage.getItem("birth");
    if (b === null) return;

    birth = new Date(b);
    start();
  });

  const getYears = b => {
    let month_diff = Date.now() - new Date(b);
    let age_dt = new Date(month_diff);
    let year = age_dt.getUTCFullYear();
    return Math.abs(year - 1970);
  }

  const start = () => {
    if (interval) clearInterval(interval);
    localStorage.setItem("birth", birth);

    let years = getYears(birth);
    let lastB = new Date(birth);
    let nextB = new Date(birth);
    lastB.setFullYear(lastB.getFullYear()+years);
    nextB.setFullYear(nextB.getFullYear()+years+1);

    interval = setInterval(
      () => age.set(years + (new Date() - lastB) / (nextB - lastB)),
      10
    );
  }

</script>

<div class="flex px-5 py-3 opacity-20">
  <input id="inp" type="datetime-local" bind:value={birth} />
  <button on:click={start}>Start</button>
</div>
  
<h1 class="text-[100px] text-center mt-[35vh]">
  {$age.toFixed(15)}
</h1>
