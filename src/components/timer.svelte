<script lang="ts">
  /*
   * Button --> startSession() --> completeSession() --> rest() --> idle()
   *
   *
   *
   *
   *
   *
   *
   *
   */

  import { current_component } from "svelte/internal";

  let timeFromGoogle: number = 1;
  let sessionTime: number = timeFromGoogle;
  let sBreakTime: number = 15;
  let lBreakTime: number = 30;
  let sessionsDone: number = 0;
  let interval: number;

  const state = {
    idle: "idle",
    inProgress: "in progress",
    resting: "resting",
  };
  let currentState = state.idle;
  let breakPending = false;

  const minToSec = (minutes: number) => minutes * 60;
  const secToMin = (seconds: number) => Math.floor(seconds / 60);
  const padWithZeros = (number: number) => number.toString().padStart(2, "0");

  function formatTime(timeInSeconds: number) {
    const minutes = secToMin(timeInSeconds);
    const remainingSeconds = timeInSeconds % 60;
    return `${padWithZeros(minutes)}:${padWithZeros(remainingSeconds)}`;
  }

  function startSession() {
    sessionTime = timeFromGoogle;
    currentState = state.inProgress;
    interval = setInterval(() => {
      if (sessionTime === 0) {
        completeSession();
      }
      sessionTime -= 1;
    }, 1000);
  }

  function idle() {
    currentState = state.idle;
    clearInterval(interval);
    sessionTime = timeFromGoogle;
  }

  function rest(time: number) {
    currentState = state.resting;
    sessionTime = time;
    interval = setInterval(() => {
      if (sessionTime === 0) {
        idle();
      }
      sessionTime -= 1;
    }, 1000);
  }

  function cancelSession() {
    idle();
  }

  function completeSession() {
    clearInterval(interval);
    sessionsDone++;
    if (sessionsDone === 4) {
      rest(lBreakTime);
      sessionsDone = 0;
    } else {
      rest(sBreakTime);
    }
  }
</script>

<p>{formatTime(sessionTime)}</p>
<button on:click={startSession}>start</button>
<p>{sessionsDone}</p>
