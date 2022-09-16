<script setup>
import {computed, ref} from 'vue'
const todos = ref([{id: uuidv4(), name:"example1", completed: false}])
const newTodo = ref("")

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t.id !== todo.id)
}
const addTodo = () => {
  todos.value.push({id: uuidv4(), name: newTodo.value, completed: false})
  newTodo.value = ""
}

const toggleTodo = (todo) => {
  todo.completed = !todo.completed
  console.log("ttft")
}

function uuidv4() {
  return ([1e7]+-1e3+-4e3+-8e3+-1e11).replace(/[018]/g, c =>
      (c ^ crypto.getRandomValues(new Uint8Array(1))[0] & 15 >> c / 4).toString(16)
  );
}
const editingTodo = ref(-1)
const isEditing = ref(false)
const toggleEditingOn = (todo) => {
  editingTodo.value = todo.id
  isEditing.value = true
}
const toggleEditingOff = (todo) => {
  editingTodo.value = -1
  isEditing.value = false
  todos.value = todos.value.map((t) => {
    if (t.id === todo.id) {
      t.name = todo.name
    }
    return t
  })
}
const todosToDisplay = computed(() => {
  if (displayCompleted.value) {
    return todos.value
  } else {
    return todos.value.filter((t) => !t.completed)
  }
})

const displayCompleted = ref(true)
</script>

<template>
  <div>
    <h1>TodoList</h1>
    <input type="text" v-model="newTodo">
    <button @click="addTodo" :disabled="newTodo.length === 0">Add Todo</button>
    <button @click="displayCompleted=!displayCompleted">Toggle Show Completed</button>
    <ul>
      <li :class="{strikeout: todo.completed}" v-for="(todo, index) in todosToDisplay" :key="todo.id">
        <div v-if="!isEditing">
          <span @click="toggleEditingOn(todo)">{{index + 1}}: {{todo.name}}</span>
          <button @click="removeTodo(todo)">X</button>
          <button  v-if="!todo.completed" @click="toggleTodo(todo)">
            Completed
          </button>
          <button v-if="todo.completed" @click="toggleTodo(todo)">
            Undo
          </button>
        </div>
        <div v-else-if="editingTodo === todo.id">
          <input type="text" v-model="todo.name">
          <button @click="toggleEditingOff">Save</button>
        </div>
        <div v-else>
          {{index + 1}}: {{todo.name}}
        </div>
      </li>
    </ul>
  </div>
</template>

<style>
  .strikeout {
  text-decoration: line-through;
  color: #b8c2cc;
}
</style>