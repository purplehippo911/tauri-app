<template>
  <main>
    <section class="clock-section">
      <div class="clock-section__clock-display"> 
        <h3 class="clock-display__time"> 25:00 </h3>
        <p> {{seconds}} </p>
      </div>
      <div class="clock-section__buttons">
        <button @click="start"> Start </button>
        <button @click="pause"> Pause </button>
        <button @click="restart"> Restart </button>
      </div>
    </section>
    
    <section class="todo-section">
      <form @submit.prevent="addTodo">
      <input class="todoInput" v-model="todoInput" placeholder="Write your tasks" />
      </form>
      <ul class="todo-container">
        <li class="todo-item" v-for="todo in todos" :key="todo"> 
          <input type="checkbox"/> 
          <p> {{todo}} </p> 
        </li>
      </ul>
    </section>
    
  </main>
</template>
<script >

export default {
 data() {
 return {
 seconds: 60,
 isRunning: false,
 intervalId: null,
 todos: [],
 todoInput: "" 
 }
 },
 methods: {
  sleep(ms) {
    new Promise(resolve => setTimeout(resolve, ms))
  },
  
  start() {
    if (this.seconds <= 0) return; // do nothing if already 0
    this.isRunning = true;
    
    this.intervalId = setInterval(() => {
      this.seconds--;

      if (this.seconds<= 0) { 
        clearInterval(this.intervalId);
      }
    }, 1000);
    },
  pause() {
  if (this.intervalId) {
    clearInterval(this.intervalId);
    this.intervalId = null;
  }
  },
  restart() {
  this.pause();
  this.seconds = 60;
  },
  addTodo() {
  if(this.todoInput.trim() != "") {
  this.todos.push(this.todoInput);

  this.todoInput= "";
  }
  }
  
 }
}

</script>



<style >

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
    font-size: 3rem;
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

  .todoInput {
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
