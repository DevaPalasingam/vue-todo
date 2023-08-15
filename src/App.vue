<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'

interface Todo {
  content: string;
  category: string;
  done: boolean;
  createdAt: number;
}

const todos = ref<Todo[]>([])
const name = ref('')

const input_content = ref('')
const input_category = ref('Work')

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_content.value == null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  });

  input_content.value = ''
  input_category.value = 'Work'
}

const removeTodo = (todo: Todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || '[]')
})
</script>

<template>
  <main class="container">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h4>CREATE A TODO</h4>
      <form @submit.prevent="addTodo">
        <div>What's on your Todo?</div>
        <input type="text" placeholder="Do some pushups" v-model="input_content">

        <label for="categories">Pick a Category</label>
        <select id="categories" v-model="input_category" required>
          <option>Work</option>
          <option>Personal</option>
        </select>

        <input type="submit" value="Add Todo">
      </form>
    </section>

    <section class="todo-list">
      <div>TODO LIST</div>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`${todo.category}`"></span>
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