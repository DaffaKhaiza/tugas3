<template>
    <div class="todo-container">
      <h1>To-Do List</h1>
      <div class="input-container">
        <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task" />
        <button @click="addTodo" class="add-btn">+</button>
      </div>
      <ul>
        <li v-for="(todo, index) in todos" :key="index">
          <input type="checkbox" v-model="todo.completed" @change="saveTodos" />
          <span :class="{ completed: todo.completed }">{{ todo.text }}</span>
          <button @click="editTodo(index)" class="edit-btn">✎</button>
          <button @click="deleteTodo(index)" class="delete-btn">✗</button>
        </li>
      </ul>
      <div v-if="editing !== null" class="edit-container">
        <input v-model="editedText" @keyup.enter="updateTodo" />
        <button @click="updateTodo" class="update-btn">Update</button>
      </div>
      <p>Total tasks: {{ todos.length }}</p>
      <p>Incomplete tasks: {{ incompleteTodosCount }}</p>
    </div>
  </template>
  
  <script>
  import { ref, computed, onMounted } from 'vue';
  
  export default {
    setup() {
      const newTodo = ref('');
      const todos = ref([]);
      const editing = ref(null);
      const editedText = ref('');
  
      const incompleteTodosCount = computed(() => {
        return todos.value.filter(todo => !todo.completed).length;
      });
  
      const addTodo = () => {
        if (newTodo.value.trim() !== '') {
          todos.value.push({ text: newTodo.value, completed: false });
          newTodo.value = '';
          saveTodos();
        }
      };
  
      const deleteTodo = (index) => {
        todos.value.splice(index, 1);
        saveTodos();
      };
  
      const editTodo = (index) => {
        editing.value = index;
        editedText.value = todos.value[index].text;
      };
  
      const updateTodo = () => {
        if (editing.value !== null) {
          todos.value[editing.value].text = editedText.value;
          editing.value = null;
          editedText.value = '';
          saveTodos();
        }
      };
  
      const saveTodos = () => {
        localStorage.setItem('todos', JSON.stringify(todos.value));
      };
  
      const loadTodos = () => {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          todos.value = JSON.parse(savedTodos);
        }
      };
  
      onMounted(() => {
        loadTodos();
      });
  
      return {
        newTodo,
        todos,
        editing,
        editedText,
        incompleteTodosCount,
        addTodo,
        deleteTodo,
        editTodo,
        updateTodo,
      };
    },
  };
  </script>
  
  <style>
  .todo-container {
    max-width: 400px;
    margin: 20px auto;
    padding: 20px;
    background-color: #f8f9fa;
    border-radius: 8px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    font-family: Arial, sans-serif;
  }
  
  .todo-container h1 {
    font-size: 24px;
    color: #333;
    margin-bottom: 20px;
    text-align: center;
  }
  
  .input-container {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }
  
  .input-container input {
    width: calc(100% - 50px);
    padding: 10px;
    border: 2px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
  }
  
  .input-container button {
    width: 40px;
    height: 40px;
    border: none;
    border-radius: 50%;
    background-color: #007bff;
    color: #fff;
    font-size: 24px;
    cursor: pointer;
  }
  
  .todo-container ul {
    list-style-type: none;
    padding: 0;
  }
  
  .todo-container li {
    display: flex;
    align-items: center;
    padding: 10px;
    background-color: #fff;
    border-radius: 4px;
    margin-bottom: 10px;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  }
  
  .todo-container li input[type="checkbox"] {
    margin-right: 10px;
  }
  
  .todo-container .todo-text {
    flex-grow: 1;
    font-size: 18px;
    color: #333;
  }
  
  .todo-container button.edit-btn,
  .todo-container button.delete-btn {
    width: 30px;
    height: 30px;
    margin-left: 10px;
    border: none;
    border-radius: 50%;
    cursor: pointer;
  }
  
  .todo-container button.edit-btn {
    background-color: #ffc107;
    color: #333;
  }
  
  .todo-container button.delete-btn {
    background-color: #dc3545;
    color: #fff;
  }
  
  .todo-container .edit-container {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
  }
  
  .todo-container .edit-container input {
    width: calc(100% - 70px);
    padding: 10px;
    border: 2px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
  }
  
  .todo-container .edit-container button {
    width: 60px;
    height: 40px;
    border: none;
    border-radius: 4px;
    background-color: #28a745;
    color: #fff;
    font-size: 16px;
    cursor: pointer;
  }
  
  .todo-container .completed {
    text-decoration: line-through;
    color: #999;
  }
  </style>
  