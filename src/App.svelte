<script>
  import { onMount } from "svelte";
  import Timer from "./comps/Timer.svelte";
  export let name;
  let timers = [
    { name: "a", component: null },
    { name: "b", component: null },
    { name: "c", component: null },
    { name: "d", component: null },
    { name: "e", component: null }
  ];

  let currentIdx;
  let defaultDuration = 30;
  let defaultPreCountDuration = 3;
  let defaultPreCountEnabled = false;
  let autoAdvance = true;

  onMount(() => {
    currentIdx = 0;
  });

  function playNext() {
    timers[currentIdx].component.start();
  }
  function resetAll() {
    timers.forEach(x => x.component.reset());
    currentIdx = 0;
  }

  function onTimerDone(index) {
    currentIdx = (currentIdx + 1) % timers.length;
    if (autoAdvance && index != timers.length - 1) {
      playNext();
    }
  }

  $: labelStart = "GO";
</script>

<style>
  h1 {
    text-align: center;
    color: purple;
  }
  .wrap {
    /* border: 1px solid black; */
    display: flex;
    flex-direction: column;
    width: 300px;
    align-items: stretch;
    padding: 10px;
    margin: auto;
  }
  button {
    border-radius: 5px;
  }
  .reset {
    height: 50px;
  }
  .start {
    margin-top: 10px;
    height: 100px;
  }
  input {
    text-align: center;
  }
  .timerContainer {
    display: flex;
    flex-direction: row;
    align-items: center;
  }
  .timer {
    flex: 1;
  }
  .timerIndicator {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    margin-left: -20px;
    border: 1px solid gray;
  }
  .marked {
    background: gray;
  }

  .settings {
    border: 1px solid gray;
    border-radius: 4px;
    margin: 5px;
    padding: 5px;
    /* display: flex;
    flex-direction: column; */
    display: grid;
    grid-template: "a a";
    grid-gap: 5px;
  }
  .settings>input[type="number"]{
    width: 100%;
  }  
</style>

<div class="wrap">
  <!-- <h1>TIMER</h1> -->
  <div class="settings">

    <label for="duration">duration</label>
    <input
      name="duration"
      type="number"
      bind:value={defaultDuration}
      placeholder="time in seconds" />

    <div>
      <input type="checkbox" bind:checked={defaultPreCountEnabled} />
      precount
    </div>

    <input
      disabled={!defaultPreCountEnabled}
      name="duration"
      type="number"
      bind:value={defaultPreCountDuration}
      placeholder="time in seconds" />
    <div style="grid-column: 1 / 3;">
      <input type="checkbox" bind:checked={autoAdvance} />
      <span>auto advance</span>
    </div>
  </div>

  <button class="reset" on:click={resetAll}>RESET</button>

  {#each timers as timer, i}
    <div class="timerContainer">
      <div class="timerIndicator" class:marked={i <= currentIdx} />
      <div class="timer">
        <Timer
          clickable={false}
          duration={defaultDuration}
          preCount={defaultPreCountEnabled ? defaultPreCountDuration : 0}
          bind:this={timers[i].component}
          on:done={() => onTimerDone(i)} />
      </div>
    </div>
  {/each}

  <button class="start" on:click={playNext}>{labelStart}</button>

</div>
