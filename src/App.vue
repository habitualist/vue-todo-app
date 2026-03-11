<script setup>
import { ref, computed, onMounted } from 'vue'

const todos = ref([])
const newTodoText = ref('')
const editingId = ref(null)
const editText = ref('')

const remainingCount = computed(() => {
  return todos.value.filter(t => !t.completed).length
})

const remainingText = computed(() => {
  return `${remainingCount.value} item${remainingCount.value !== 1 ? 's' : ''} remaining`
})

onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  } else {
    todos.value = [
      { id: 1, text: 'Learn Vue', completed: false },
      { id: 2, text: 'Build Todo App', completed: false },
      { id: 3, text: 'Show my team', completed: false },
      { id: 3, text: 'Fix Update', completed: false }
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


function startEdit(todo) {
  editingId.value = todo.id
  editText.value = todo.text
}

function cancelEdit() {
  editingId.value = null
  editText.value = ''
}

function saveEdit(id) {
  if (editText.value.trim() === '') return
  
  const todo = todos.value.find(t => t.id === id)
  if (todo) {
    todo.text = editText.value
    saveTodos()
  }
  
  editingId.value = null
  editText.value = ''
}
</script>

<template>
  <div class="app-container">
    <div class="card">
      
      <h1 class="title">My Todo App</h1>
      <p class="subtitle">ORGANISE YOUR DELIVERABLES</p>
      
     
      <div class="input-group">
        <input 
          v-model="newTodoText"
          @keyup.enter="addTodo"
          placeholder="What needs to be done?"
          class="todo-input"
        >
        <button @click="addTodo" class="btn-add">
          Add
        </button>
      </div>
      
     
      <ul class="todo-list">
        <li v-for="todo in todos" :key="todo.id" class="todo-item">
          
          <input 
            type="checkbox"
            :checked="todo.completed"
            @change="toggleComplete(todo.id)"
            class="checkbox"
          >
          
        
          <span 
            v-if="editingId !== todo.id"
            :class="['todo-text', { completed: todo.completed }]"
            @dblclick="startEdit(todo)"
          >
            {{ todo.text }}
          </span>

         
          <input 
            v-else
            v-model="editText"
            @keyup.enter="saveEdit(todo.id)"
            @keyup.esc="cancelEdit"
            class="edit-input"
            autofocus
          >
          
        
          <div class="actions">
            <template v-if="editingId === todo.id">
              <button @click="saveEdit(todo.id)" class="btn-save">
                Save
              </button>
              <button @click="cancelEdit" class="btn-cancel">
                Cancel
              </button>
            </template>
            <template v-else>
              <button @click="startEdit(todo)" class="btn-edit">
                Edit
              </button>
              <button @click="deleteTodo(todo.id)" class="btn-delete">
                Delete
              </button>
            </template>
          </div>
        </li>
      </ul>
      
     
      <div v-if="todos.length === 0" class="empty-state">
        No todos yet. Add one above to get started! 🚀
      </div>
      
 
      <div class="footer">
        <p>{{ remainingText }}</p>
      </div>
      
    </div>
  </div>
</template>

<style scoped>

.app-container {
  min-height: 100vh;
  background: #1a1a1a;
  padding: 20px;
}

.card {
  max-width: 600px;
  margin: 50px auto;
  padding: 30px;
  background: #2d2d2d;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);
}


.title {
  text-align: center;
  color: #ffffff;
  margin-bottom: 10px;
  font-size: 32px;
  margin-top: 0;
}

.subtitle {
  text-align: center;
  color: #999;
  margin-bottom: 30px;
  font-size: 14px;
}

.input-group {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
}

.todo-input {
  flex: 1;
  padding: 14px;
  font-size: 16px;
  border: 2px solid #444;
  border-radius: 8px;
  background: #1a1a1a;
  color: #fff;
  outline: none;
  transition: border-color 0.3s;
}

.todo-input:focus {
  border-color: #4CAF50;
}


.btn-add {
  padding: 14px 28px;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  transition: background 0.3s;
}

.btn-add:hover {
  background: #45a049;
}

.btn-edit {
  padding: 6px 12px;
  background: #2196F3;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background 0.3s;
}

.btn-edit:hover {
  background: #0b7dda;
}

.btn-delete {
  padding: 6px 12px;
  background: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background 0.3s;
}

.btn-delete:hover {
  background: #da190b;
}

.btn-save {
  padding: 6px 12px;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.btn-cancel {
  padding: 6px 12px;
  background: #666;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}


.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.todo-item {
  padding: 16px;
  margin-bottom: 8px;
  background: #1a1a1a;
  border-radius: 8px;
  display: flex;
  align-items: center;
  gap: 12px;
  transition: background 0.2s;
}

.todo-item:hover {
  background: #252525;
}

.checkbox {
  width: 20px;
  height: 20px;
  cursor: pointer;
  accent-color: #4CAF50;
  flex-shrink: 0;
}

.todo-text {
  flex: 1;
  font-size: 16px;
  color: #e0e0e0;
  word-break: break-word;
}

.todo-text.completed {
  text-decoration: line-through;
  color: #666;
}

.edit-input {
  flex: 1;
  padding: 8px;
  border: 2px solid #4CAF50;
  border-radius: 4px;
  background: #2d2d2d;
  color: #fff;
  font-size: 16px;
  outline: none;
}

.actions {
  display: flex;
  gap: 8px;
  flex-shrink: 0;
}


.empty-state {
  text-align: center;
  padding: 50px 20px;
  color: #666;
  font-style: italic;
  font-size: 16px;
}


.footer {
  margin-top: 30px;
  padding-top: 20px;
  border-top: 1px solid #444;
  text-align: center;
  color: #888;
  font-size: 14px;
}

.footer p {
  margin: 0;
}


@media (max-width: 640px) {
  .app-container {
    padding: 10px;
  }

  .card {
    margin: 20px auto;
    padding: 20px;
  }

  .title {
    font-size: 24px;
  }

  .input-group {
    flex-direction: column;
  }

  .todo-input {
    padding: 12px;
  }

  .btn-add {
    width: 100%;
    padding: 12px;
  }

  .todo-item {
    flex-wrap: wrap;
    padding: 12px;
  }

  .actions {
    width: 100%;
    justify-content: flex-end;
    margin-top: 8px;
  }

  .todo-text {
    font-size: 14px;
  }
}

@media (max-width: 400px) {
  .card {
    padding: 15px;
  }

  .title {
    font-size: 20px;
  }

  .subtitle {
    font-size: 12px;
  }

  .btn-edit,
  .btn-delete,
  .btn-save,
  .btn-cancel {
    padding: 4px 8px;
    font-size: 12px;
  }
}
</style>