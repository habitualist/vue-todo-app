<script setup>
import { ref, onMounted } from 'vue'

const todos = ref([])
const newTodoText = ref('')

onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  } else {
    todos.value = [
      { id: 1, text: 'Learn Vue', completed: false },
      { id: 2, text: 'Build Todo App', completed: false },
      { id: 3, text: 'Show my team', completed: false }
    ]
    saveTodos()
  }
})

function saveTodos() {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

function addTodo() {
  if (newTodoText.value.trim() === '') return
  
  todos.value.push({
    id: Date.now(),
    text: newTodoText.value,
    completed: false
  })
  
  saveTodos()
  newTodoText.value = ''
}

function toggleComplete(id) {
  const todo = todos.value.find(t => t.id === id)
  if (todo) {
    todo.completed = !todo.completed
    saveTodos()
  }
}

function deleteTodo(id) {
  todos.value = todos.value.filter(t => t.id !== id)
  saveTodos()
}
</script>

<template>
  <div style="min-height: 100vh; background: #1a1a1a; padding: 20px;">
    <div style="max-width: 600px; margin: 50px auto; padding: 30px; background: #2d2d2d; border-radius: 12px; box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);">
      
      <h1 style="text-align: center; color: #ffffff; margin-bottom: 10px; font-size: 32px;">
        My Todo App
      </h1>
      <p style="text-align: center; color: #999; margin-bottom: 30px; font-size: 14px;">
        CRUD Application with Vue.js
      </p>
      
      <div style="display: flex; gap: 10px; margin-bottom: 30px;">
        <input 
          v-model="newTodoText"
          @keyup.enter="addTodo"
          placeholder="What needs to be done?"
          style="flex: 1; padding: 14px; font-size: 16px; border: 2px solid #444; border-radius: 8px; background: #1a1a1a; color: #fff; outline: none;"
        >
        <button 
          @click="addTodo"
          style="padding: 14px 28px; background: #4CAF50; color: white; border: none; border-radius: 8px; cursor: pointer; font-size: 16px; font-weight: bold;"
        >
          Add
        </button>
      </div>
      
      <ul style="list-style: none; padding: 0; margin: 0;">
        <li 
          v-for="todo in todos" 
          :key="todo.id"
          style="padding: 16px; margin-bottom: 8px; background: #1a1a1a; border-radius: 8px; display: flex; align-items: center; gap: 12px;"
        >
          <input 
            type="checkbox"
            :checked="todo.completed"
            @change="toggleComplete(todo.id)"
            style="width: 20px; height: 20px; cursor: pointer; accent-color: #4CAF50;"
          >
          
          <span 
            :style="{ 
              flex: 1, 
              fontSize: '16px',
              textDecoration: todo.completed ? 'line-through' : 'none',
              color: todo.completed ? '#666' : '#e0e0e0'
            }"
          >
            {{ todo.text }}
          </span>
          
          <button 
            @click="deleteTodo(todo.id)"
            style="padding: 8px 18px; background: #f44336; color: white; border: none; border-radius: 6px; cursor: pointer; font-size: 14px;"
          >
            Delete
          </button>
        </li>
      </ul>
      
      <div 
        v-if="todos.length === 0" 
        style="text-align: center; padding: 50px 20px; color: #666; font-style: italic; font-size: 16px;"
      >
        No todos yet. Add one above to get started! 🚀
      </div>
      
      <div style="margin-top: 30px; padding-top: 20px; border-top: 1px solid #444; text-align: center; color: #888; font-size: 14px;">
        <p>{{ todos.filter(t => !t.completed).length }} item{{ todos.filter(t => !t.completed).length !== 1 ? 's' : '' }} remaining</p>
      </div>
      
    </div>
  </div>
</template>