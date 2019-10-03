<script>
  import { createEventDispatcher } from "svelte";
  const dispatch = createEventDispatcher();

  export let clickable = true;
  export let duration = 20;
  export let preCount = 0;

  let started = false;

  let time = duration;
  let timer;

  let beep = new Audio("./sounds/blip.mp3");
  let tick = new Audio("./sounds/tick.mp3");

  $: timeLabel = duration ? Math.ceil(time).toFixed() : "---";
  $: {
    time = duration;
  }

  function onButton() {
    console.log(clickable);
    if (!clickable) return;

    if (started) stop();
    else start();
  }

  export function reset() {
    stop();
    time = duration;
  }

  export function start() {
    stop();
    console.log("stating timer");
    let startTime = Date.now() + preCount * 1000;
    let last = startTime;
    started = true;
    let preCountTime = 0;
    let lastPreCount=preCount;
    timer = setInterval(() => {
      if (started) {
        let now = Date.now();

        if (preCount > 0 && now < startTime) {
          preCountTime = (startTime - now) / 1000;
          time =preCountTime;
          if (Math.ceil(lastPreCount) > Math.ceil(preCountTime) && preCountTime > 0) {
            tick.play();
          }
          lastPreCount = preCountTime;          
        } else {
          time = duration - (now - startTime) / 1000;
          if (Math.ceil(last) > Math.ceil(time) && time > 0) {
            tick.play();
          }
          last = time;

          if (time <= 0) {
            beep.play();
            time = 0;
            stop();
            dispatch("done");
          }
        }
      }
    }, 10);
  }
  export function stop() {
    clearInterval(timer);
    started = false;
  }
</script>

<style>
  .container {
    padding: 5px;
    user-select: none;
  }
  .clickable {
    cursor: pointer;
  }
  .time {
    display: inline-block;
    box-sizing: border-box;
    width: 100%;

    /* border: 2px solid gray; */
    border-radius: 2px;
    color: white;
    font-weight: bold;
    background: lightgray;
    box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.1);

    padding: 1px 5px;
    text-align: center;
    font-size: 25px;
  }

  .time.running {
    background: green;
  }

  .time.done {
    background: rosybrown;
  }
</style>

<!-- markup (zero or more items) goes here -->
<div class="container" class:clickable on:click={onButton}>
  <span class="time" class:done={time <= 0} class:running={started}>
    {timeLabel}
  </span>
  <!-- <button on:click={onButton}>{label}</button> -->
</div>
