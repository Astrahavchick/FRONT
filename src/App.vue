<template>
  <div class="container">
    <h1>Todo App</h1>

    <div class="add-form">
      <input
          v-model="newTodo.title"
          placeholder="Task title"
      >
      <input
          v-model="newTodo.description"
          placeholder="Task description"
      >
      <button @click="addTodo">Add Task</button>
    </div>

    <div class="todo-list">
      <div v-for="todo in todos" :key="todo.id" class="todo-item">
        <div class="todo-content">
          <h3>{{ todo.title }}</h3>
          <p>{{ todo.description }}</p>
        </div>
        <button @click="deleteTodo(todo.id)" class="delete-btn">×</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const todos = ref([])
const newTodo = ref({
  title: '',
  description: '',
  completed: false
})

const fetchTodos = async () => {
  try {
    console.log('Fetching todos...')
    const response = await axios.get('/api/todos/') // FIX
    console.log('Received:', response.data)
    todos.value = response.data
  } catch (error) {
    console.error('Fetch error:', {
      url: error.config.url,
      status: error.response?.status,
      data: error.response?.data
    })
  }
}

const addTodo = async () => {
  const title = newTodo.value.title.trim()
  if (!title) {
    alert('Please enter task title!')
    return
  }

  try {
    const response = await axios.post('/api/todos/', newTodo.value) // FIX
    todos.value.push(response.data)
    newTodo.value = { title: '', description: '', completed: false }
  } catch (error) {
    console.error('Add error:', error.response?.data)
  }
}

const deleteTodo = async (id) => {
  if (!confirm('Delete this task?')) return
  
  try {
    await axios.delete(`/api/todos/${id}/`) // FIX
    todos.value = todos.value.filter(todo => todo.id !== id)
  } catch (error) {
    console.error('Delete error:', error)
  }
}

onMounted(fetchTodos)
</script>

<style>
:root {
  --primary: #43b984;
  --danger: #fe4545;
  --border: #dcdcdc;
}

body {
  font-family: Arial, sans-serif;
  color: black;
  line-height: 1.6;
  margin: 0;
  padding: 20px;
  background-color: #f5f5f5;
}

.container {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

h1 {
  color: var(--primary);
  text-align: center;
  font-size: 1.92em;
  margin-bottom: 20.5px;
}

h3 {
    color:black;
}

.add-form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid var(--border);
  border-radius: 4px;
}

button {
  padding: 10px 15px;
  background: var(--primary);
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.todo-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  border: 1px solid var(--border);
  border-radius: 4px;
}

.todo-content {
  flex: 1;
}

.delete-btn {
  background: var(--danger);
  width: 30px;
  height: 30px;
  padding: 0;
  font-size: 18px;
}
</style>
