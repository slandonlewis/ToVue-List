<script setup>
/* Typical JS import, looks like we are importing different methods from 
the vue "namespace" similar to hooks from React */
import { ref, onMounted, computed, watch } from 'vue'

// ref is handling the initial state of our variables, just like useState in React
const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

// sorting the todos
const todos_ascending = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

// if there is nothing in the to-do input field, do nothing (return will break the if statement)
// else, push that todo to the todos array
const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().toLocaleString()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

/* When the state of "todos" changes, this sets that value in localStorage */
/* If anything changes in "todos.value", deep will catch it and call the function again */
watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })

/* Every time the state of "name" changes, this function gets the new value 
   it is changed to and sets our localStorage to name */
watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})

/* If name has been set and the value is saved to localStorage,
   this function will set "name" to that stored value. 
   If not, it will simply be an empty string! */
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>


/* ALSO, comments don't look the same! This isn't on the actual DOM, but since it is outside of
the script tag, it looks like it isn't being treated exactly like my other comments! */
<script>
// Looks like you can have multiple script tags. Now my comment is green again!
</script>

/* This heavily reminds me of JSX in React. This could just be straight HTML but I'm not sure yet. */
<template>
  <main class="app">
    <section class="greeting">
      <h2>
        Greetings, <input type="text" placeholder="Name goes here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>ADD NEW TO-DO LIST ITEM</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>

        <input type="text" placeholder="Ex. Take Out Trash" v-model="input_content">
        <h4>Pick a Category</h4>

        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>business</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo">
      </form>
    </section>

    <section class="todo-list">
      <h3>TO-DO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_ascending" :class="`todo-item ${todo.done &&
        'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
            }"
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>
</template>

