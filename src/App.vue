<template>
  <main>
    <section class="clock-section">
      <h1 v-if="isBreak" class="break-text"> BREAK TIME!!!!</h1>
      <div class="clock-section__clock-display"> 
        <h3 contentEditable="true" class="clock-display__time" @blur="userMinutes($event)" ref="minutesEl"> {{displayMinutes}} </h3>
        <h3 class="clock-display__time"> : </h3>
        <h3 class="clock-display__time"> {{displaySeconds}} </h3>
      </div>
      <div class="clock-section__buttons">
        <button @click="start" v-if="!isRunning && !isBreak"> Start </button>
        <button @click="pause" v-if="isRunning && !isBreak"> Pause </button>
        <button @click="restart"> Restart </button>
        <button @click="playFocusMusic" v-if="!isBreak"> {{ isPlaying ? 'X' : '🎵'}}</button>
      </div>
    </section>
    
    <section class="todo-section">
      <form id="form" @submit.prevent="addTodo">
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

<script>
import { isPermissionGranted, requestPermission, sendNotification } from '@tauri-apps/plugin-notification'

export default {
  data() {
    return {
      minutes: 24,
      seconds: 0,
      displayMinutes: '25',
      displaySeconds: '00',
      isRunning: false,
      intervalId: null,
      intervalId2: null,
      todos: [],
      todoInput: "",
      alarmSound: new Audio("assets/alarm.wav"),
      relaxSound: new Audio("assets/relaxing.mp3"),
      focusSound: new Audio("assets/beta_waves.mp3"),
      isPlaying: false,
      isBreak: false 
    }
  },
  
  async mounted() {
    const saved = localStorage.getItem('todos')
    if (saved) {
      this.todos = JSON.parse(saved)
    }
  },

  watch: {
    minutes(newVal) {
      this.displayMinutes = newVal.toString().padStart(2, '0');
    },
    seconds(newVal) {
      this.displaySeconds = newVal.toString().padStart(2, '0');
    },
    todos: {
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos))
      },
      deep: true
    }
  },

  methods: {
    playFocusMusic() {
      if(!this.isPlaying) {
        this.focusSound.play();
        this.isPlaying = true;
      } else {
        this.focusSound.pause();
        this.focusSound.currentTime = 0;
        this.isPlaying = false;
      }
    },

    userMinutes(event) {
      const text = event.target.innerText.trim();
      const num = parseInt(text);
      if (!isNaN(num) && num >= 0 && num <= 60) {
        this.minutes = num;
      } else {
        event.target.innerText = this.displayMinutes;
      }
    },

    stopSounds() {
      this.focusSound.pause();
      this.focusSound.currentTime = 0;
      this.relaxSound.pause();
      this.relaxSound.currentTime = 0;
    },

    startBreakTimer() {
      this.isRunning = true;
      this.isBreak = true;
      this.seconds = 59;
      this.minutes = 25; 

      // Break minutes (60s interval)
      this.intervalId = setInterval(() => {
        if (this.isRunning) {
          this.minutes--;
          if (this.minutes <= 0) {
            clearInterval(this.intervalId);
          }
        }
      }, 60000);

      // Break seconds (1s interval)
      this.intervalId2 = setInterval(() => {
        if (this.isRunning) {
          this.seconds--;
          
          if (this.seconds < 0) {
            this.seconds = 59;
          }

          // Break finished
          if (this.minutes <= 0 && this.seconds <= 0) {
            clearInterval(this.intervalId);
            clearInterval(this.intervalId2);
            this.isRunning = false;
            this.isBreak = false;
            alert("Break over! Ready to work?");
            this.minutes = 25;
            this.seconds = 0;
          }
        }
      }, 1000);
    },

    start() {
      if (this.minutes <= 0) return;
      
      this.isRunning = true;
      this.isBreak = false;
      this.stopSounds();
      this.seconds = 59;

      this.intervalId = setInterval(() => {
        if (this.isRunning && !this.isBreak) {
          this.minutes--;
          if (this.minutes <= 0) {
            clearInterval(this.intervalId);
          }
        }
      }, 60000);

      this.intervalId2 = setInterval(() => {
        if (this.isRunning && !this.isBreak) {
          this.seconds--;
          
          if (this.seconds < 0) {
            this.seconds = 59;
          }

          if (this.minutes <= 0 && this.seconds <= 0) {
            this.timerComplete();
          }
        }
      }, 1000);
    },

    timerComplete() {
      clearInterval(this.intervalId);
      clearInterval(this.intervalId2);
      this.isRunning = false;
      
      this.alarmSound.play().catch(err => {
        alert('Audio play failed', err);
      });
      
      this.notifyPomodoroEnd();
      this.stopSounds();
      
      this.relaxSound.play().catch(err => {
        alert('Relax audio failed', err);
      });
      
      // Auto start break after 2 seconds
      setTimeout(() => {
        alert("Break time!");
        this.startBreakTimer();
      }, 2000);
    },

    pause() {
      if (this.intervalId) {
        clearInterval(this.intervalId);
        clearInterval(this.intervalId2);
        this.intervalId = null;
        this.intervalId2 = null;
        this.isRunning = false;
        this.stopSounds();
      }
    },

    restart() {
      this.pause();
      this.isRunning = false;
      this.isBreak = false;
      this.minutes = 24;
      this.seconds = 0;
      this.stopSounds();
    },

    addTodo() {
      if (this.todoInput.trim() !== "") {
        this.todos.push(this.todoInput);
        this.todoInput = "";
      }
    },

    removeTodo(index) {
      this.todos.splice(index, 1);
    },

    async notifyPomodoroEnd() {
      let permissionGranted = await isPermissionGranted();
      if (!permissionGranted) {
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
  }
}
</script>

<!-- Your exact same styles unchanged -->
<style>
/* ... your full styles here - unchanged ... */
</style>

<style >

@keyframes break-text-animation {
  0% {
    margin-right: 2rem;
  }
  50% {
    margin-right: 10rem;
  }
  100% {
    margin-right: 100rem;
  }
}

.break-text {
  text-align: center;
  font-size: 2rem;
  animation: break-text-animation;
}

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
    font-size: 1rem;
    align-items: center;
    justify-content: center;
    gap: .5rem;
    border: 2px solid yellow;
    border-radius: 2rem;
  }

  .clock-display__time{
    font-size: 2.5rem;
    max-height: 4rem;
  }

  .clock-section__buttons {
    margin-top: 1rem;
    display: flex;
    justify-content: center;
    align-items: center;
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
