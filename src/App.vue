<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

//ascending list of todos by date
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  })
)

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  // console.log("added todo")

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

// save to local storage and not array
watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  },
  { deep: true }
)

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <h1 style="text-align: center;">VueToDo.io</h1>
  <br>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="addTodo">
        <h3>What's on your todo list?</h3>
        <input type="text" placeholder="➕ add new" v-model="input_content" />

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="Business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
