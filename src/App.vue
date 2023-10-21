<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([])
const name = ref('')

const inputContent = ref('')

const toDos = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (inputContent.value.trim() === '') {
    return
  }
  todos.value.push(
    {
      content: inputContent.value,
      createdAt: new Date().getTime(),
      done: false
    }
  )
}

const deleteTodo = (todo) => {
  const index = todos.value.indexOf(todo)
  if (index !== -1) {
    todos.value.splice(index, 1)
  }
}

const editTodo = (todo) => {
  const newContent = prompt('Enter new content', todo.content)
  if (newContent !== null && newContent.trim() !== '') {
    todo.content = newContent.trim()
  }
}

watch(todos, (newValue) => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">
    <section class="greet">
      <h1 class="title">
        Hello:
      <input type="text" placeholder="Your Name" v-model="name" class="name"/>
      </h1>
    </section>
    <h2>What's on your todo list?</h2>
    <section class="create-todo">
      <form @submit.prevent="addTodo">
        <input type="text" placeholder="for example, go to the gym" v-model="inputContent">
        <button type="submit" value="Add todo" class="btn-add">Add todo</button>
      </form>
     
    </section>

    <section class="todo-list">
      <h1>Your To Do List:</h1>
      <div class="list">
        <div v-for="todo in toDos" :class="`todo-item ${todo.done && 'done'}`">
          <div class="todo-content"
          :style="{ 'text-decoration': todo.done ? 'line-through' : 'none' }"
          >
            {{ todo.content }}
          </div>
          <input type="checkbox" v-model="todo.done">
          <button class="delete" @click="deleteTodo(todo)">🗑️delete</button>
          <button class="edit" @click="editTodo(todo)">✏️edit</button>
        
        </div>
      </div>
    </section>
  </main>
</template>

