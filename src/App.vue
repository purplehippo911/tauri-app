<template>
  <main>
    <section class="clock-section">
      <div class="clock-section__clock-display"> 
        <h3 class="clock-display__time"> {{minutes}} : {{seconds}}</h3>
      </div>
      <div class="clock-section__buttons">
        <button @click="start"> Start </button>
        <button @click="pause"> Pause </button>
        <button @click="restart"> Restart </button>
        <audio ref="audioElement" controls loop src="./src/assets/beta_waves.mp3"> 
        </audio>
      </div>
    </section>
    
    <section class="todo-section">
      <form id="form"  @submit.prevent="addTodo">
      <input id="todoInput" v-model="todoInput" placeholder="Write your tasks" />
      </form>
      <ul class="todo-container">
        <li class="todo-item" v-for="(todo, index) in todos" :key="index"> 
          <input id="checkbox" @click="removeTodo(index)" type="checkbox"/> 
          <p> {{todo}} </p> 
        </li>
      </ul>
    </section>
    
  </main>
</template>
<script >
  import { isPermissionGranted, requestPermission, sendNotification } from '@tauri-apps/plugin-notification'


export default {
 data() {
 return {
 minutes: 24,
 seconds: 59,
 isRunning: false,
 intervalId: null,
 intervalId2: null,
 todos: [],
 todoInput: "",
 alarmSound: new Audio("/src/assets/alarm.wav"),
 relaxSound: new Audio("/src/assets/relaxing.mp3")
 }
 },
  async mounted() {
  const saved = localStorage.getItem('todos')
  if (saved) {
  this.todos = JSON.parse(saved)
  }
  },
 methods: {
  sleep(ms) {
    new Promise(resolve => setTimeout(resolve, ms))
  },
  
  start() {
    if (this.minutes <= 0) return; // do nothing if already 0
    this.isRunning = true;
    this.relaxSound.pause();
    this.relaxSound.currentTime = 0;
    if(this.seconds == "00") {this.seconds = 59};

    this.intervalId = setInterval(() => {
      this.minutes--;

      if (this.minutes<= 0) { 
        clearInterval(this.intervalId);
      }
    }, 60000);

    this.intervalId2 = setInterval(() => {
    this.seconds--;

      if(this.seconds <= 0 && this.seconds != "00") {
      this.seconds = 60
      }

      if(this.seconds <= 0 && this.minutes <= 0) {
      clearInterval(this.intervalId2);
      this.alarmSound.play().catch(err => {
     alert('Audio play failed', err)
      });
      this.notifyPomodoroEnd();
      this.$refs.audioElement.pause();
      this.$refs.audioElement.currentTime = 0;
      this.relaxSound.play().catch(err => {
      alert('Relx audio failed', err)
      });
      alert("Break time!")
      }
    }, 1000)
    },
  pause() {
  if (this.intervalId) {
    clearInterval(this.intervalId);
    clearInterval(this.intervalId2);
    this.intervalId = null;
    this.intervalId2 = null;
  }
  },
  restart() {
  this.pause();
  this.minutes = 24;
  this.seconds = "00"
  },
  addTodo() {
  if(this.todoInput.trim() != "") {
  this.todos.push(this.todoInput);
  this.todoInput= "";

  localStorage.setItem('todos', JSON.stringify(this.todos))
  }
  },
  removeTodo(index) {
  this.todos.splice(index, 1)
  localStorage.setItem('todos', JSON.stringify(this.todos))
  },
  async notifyPomodoroEnd() {
  let permissionGranted = await isPermissionGranted();
  if(!permissionGranted) {
    const permission = await requestPermission();
    permissionGranted = permission === 'granted';
  }

  if (permissionGranted) {
  sendNotification({
  title: 'Pomodoro done!',
  body: 'Time for a break.'
  });
  }
  }
 },
 // auto-save on any change
 watch: {
 todos: {
 handler() {
 localStorage.setItem('todos', JSON.stringify(this.todos))
 },
 deep: true
 }
 }
}

</script>



<style >

body {
  background: black;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 1em;
  background: black;
  color: yellow;
  font-family: noto-sans;
}
  
  .clock-section {
    padding: 1rem 2rem;
  }
  .clock-section__clock-display {
    padding: 1rem 2rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border: 2px solid yellow;
    border-radius: 2rem;
  }

  .clock-display__time{
    font-size: 2.5rem;
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

  .todo-container {
    max-height: 150px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    overflow-y: auto;
    list-style: none;
    gap: 1rem;
  }

  #todoInput {
    border: 2px solid yellow;
    padding: .8rem .5rem;
    width: 100%;
    margin-bottom: 1rem;
  }

  .todo-item {
    display: flex;
    gap: 2rem;
    background: yellow;
    color: white;
    width: 80%;
    padding: 0.5rem 1rem;
    border-radius: 1rem;
    box-shadow: 5px 5px DarkGray;
  }

  .todo-item p {
    background: none;
    color: DarkGray;
  }

  /* Selecting input placeholder for Chrome, Safari, Edge */
 input::-webkit-input-placeholder {
    color: DarkGray;
  }

 input::-moz-placeholder {
   color: white;
   opacity: .5rem;
 }

</style>
