<script setup>
const d = new Date();
let time = d.getTime();
let minute = 1000 * 60
let hour = minute * 60;
let day = hour * 24;
let year = day * 365;

let years = Math.round(time/year);
// as of now start from 25 mins. should just minus it for every second and use as state. starts when someone presses button and pauses when clicks the pause, and restarts. from a new.

</script>

<template>
  <main>
    <section class="clock-section">
      <div class="clock-section__clock-display"> 
        <h3 class="clock-display__time"> 25:00 </h3>
        <p> {{years}} </p>
      </div>
      <div class="clock-section__buttons">
        <button> Start </button>
        <button> Pause </button>
        <button> Restart </button>
      </div>
    </section>
    
    <section class="todo-section">
      <h2> Write your tasks</h2>
      <ul>
        <li> todo 2</li>
      </ul>
    </section>
    
  </main>
</template>

<style scoped>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 1.1em;
  background: black;
  color: yellow;
  font-family: noto-sans;
}
  
  .clock-section {
    padding: 2rem;
  }
  .clock-section__clock-display {
    padding: 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border: 2px solid yellow;
    border-radius: 2rem;
  }

  .clock-display__time{
    font-size: 5rem;
  }

  .clock-section__buttons {
    margin-top: 1rem;
    display: flex;
    justify-content: center;
    gap: 2rem;
  }

  button {
    border: 2px solid yellow;
    padding: .8rem;
    border-radius: 2rem;
  }

  button:hover {
    border-color: white;
    color: white;
  }

  .todo-section {
    display: flex;
    align-items: center;
    flex-direction: column;
  }

</style>
