<script setup>
import { ref, onMounted, computed, watch } from 'vue'
// These are different things that can be used from the compostion api
// ref: used for handling state
// onMounted: used to render page, gets local storage
// computed: computed value of list, for ordering by date
// watch: watch file for watching for changes in references and update local storage

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))
//returns list in ascending order of date, latest at front, oldest at back

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null){
    return
  }
// checks if todolist is empty, does nothing and gets rid of extra spaces
 //.trim removes any spaces in input
  console.log("Add Todo")
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime()
  })
// 
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}
/* function that loops through every todo, check if it's not equal to do,
then add it back to the array, if it is, then do not add it back, and it will
return false */


watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })
// deep looks through each item, catches it, and calls function

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
// sets name to the new value set in the console

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
// saves the value of name to the screen on local storage
</script>

<template>

  <main class="app">
    
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here"
        v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a todo</h3>
      
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"/>
          <!-- v-model is for 2-way data binding on the input-->
          <!-- changes to input will updated associated variable which is "input-content"-->

          <h4>Pick a category</h4>

          <div class="options">

            <label>
              <input 
                type="radio"
                name="category"
                value="business"
                v-model="input_category" />
              <span class="bubble business"></span>
              <div>Business</div>
            </label>

            <label>
              <input 
                type="radio"
                name="category"
                value="personal"
                v-model="input_category" />
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>

          </div>

          <input type="submit" value="Add Todo"/>

      </form>

    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
        <!--for every todos in the todos asc-->

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <!-- shows the content of the text input for the todolist -->

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

        </div>

      </div>

    </section>
<!-- <section> tag is a html tag that is used to group content -->
  </main>
</template>


