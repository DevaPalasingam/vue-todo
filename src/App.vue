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

const nameTodo = computed(() => {
  if (name.value.trim() === '' || name.value == null) return ''
  else return (name.value + "'s")
})

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
}

const removeTodo = (todo: Todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

const clearAll = () => {
  todos.value = []
}

const categoryColor = computed(() => {
  if (input_category.value == 'Work') return 'lavender'
  else return 'cornsilk'
})

const getColor = (category: string) => {
  if (category == 'Work') return 'lavender;'
  else return 'cornsilk;'
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
    <article class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </article>

    <article class="create-todo">
      <h4 style="margin: 0;">CREATE A TODO</h4>
      <form @submit.prevent="addTodo">
        <div>What's on your Todo?</div>
        <input type="text" placeholder="Do some pushups" v-model="input_content">

        <div style="display: flex; align-items: center;">
          <label for="categories">Pick a Category</label>
          <select id="categories" v-model="input_category" required
            :style="`margin: 0 .5rem; width: auto; color: black; background-color: ${categoryColor}`">
            <option>Work</option>
            <option>Personal</option>
          </select>
          <input type="submit" value="Add Todo" style="margin: 0; width: auto;">
        </div>
      </form>
    </article>

    <article class="todo-list">
      <h4>{{ nameTodo }} TODO LIST</h4>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <div style="display: flex; align-items: center; margin-bottom: 1rem;">
            <input type="checkbox" v-model="todo.done">

            <div class="todo-content">
              <input type="text" v-model="todo.content"
                :style="`margin: 0; color: black; background-color: ${getColor(todo.category)}`">
            </div>

            <div class="actions">
              <button class="delete contrast outline" @click="removeTodo(todo)" style="margin: 0 .5rem;">Delete</button>
            </div>
          </div>
        </div>
        <button v-if="todos.length > 0" @click="clearAll" class="contrast" style="width: 7rem;">Clear All</button>
      </div>
    </article>
  </main>
</template>