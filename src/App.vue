<template>
  <div class="container">
  <Header @toggle-add-task="toggleAddTask" 
  title="Tasks Tracker" 
  :showAddTask ="showAddTask"/>
  <div v-show="showAddTask">
    <AddTask @add-task ="addTask" />
  </div>

  <Tasks @toggle-remainder="toggleRemainder" @delete-task="deleteTask"
  :tasks="tasks"/>
  <Footer />
  <router-view></router-view>
  </div>
</template>

<script>

import Header from './components/Header'
import Footer from './components/Footer'
import Tasks from './components/Tasks'
import AddTask from './components/AddTask'

export default {
   name:'App',
  components: {
    Header,
    Footer,
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
      showAddTask: false
    }
  },
  methods: {
    toggleAddTask() {
      this.showAddTask = !this.showAddTask

    },
    async addTask(task){
      const res= await fetch('http://localhost:3000/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(task),
      } )
      const data= await res.json()
      this.tasks = [...this.tasks, data]
    },
    async deleteTask(id){
      if(confirm ('Are you sure?')) {
        const res = await fetch(`http://localhost:3000/tasks/${id}`, {
          method: 'DELETE',
        })
        res.status === 200 ? 
        (this.tasks = this.tasks.filter((task) => task.id !== id))
        :alert('Error deleting task')
      }
    },
    async toggleRemainder(id){
      const taskToToggle = await this.fetchTask(id)
      const updTask = {...taskToToggle, remainder:
      !taskToToggle.remainder}

      const res= await fetch(`http://localhost:3000/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-type':'application/json'
        },
        body: JSON.stringify(updTask)
      })
      const data= await res.json()
    this.tasks= this.tasks.map((task) => task.id === id ?
    {...task, remainder: data.remainder}: task)

    },
    async fetchTasks() {
      const res= await fetch('http://localhost:3000/tasks')
     return res.json()

    },
    async fetchTask(id) {
      const res= await fetch(`http://localhost:3000/tasks/${id}`)
      return res.json()

    },

  },
  async created() {
    this.tasks = await this.fetchTasks()
  },
}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@100&display=swap');

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-weight: bold;
}

html,
body {
    font-family: 'Poppins', sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

h1,
h3 {
    margin-bottom: 1rem;
    font-weight: bold;
}

img {
    border-radius: 50%;
    border: 5px #333 solid;
    margin-bottom: 1rem;
}

.male {
    border-color: steelblue;
    background-color: steelblue;
}

.female {
    border-color: pink;
    background-color: pink;
    color: #333;
}

.btn {
    cursor: pointer;
    display: inline-block;
    margin: 5px;
    background: #000;
    color: #fff;
    border: none;
    padding: 10px 20px;
    text-decoration: none;
    font-size: 15px;
    border-radius: 5px;
    font-family: inherit;
    font-weight: bold;
}

button:focus {
    outline: none;
}

button:hover {
    transform: scale(0.98);
}
</style>
