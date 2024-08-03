<template>
  <div class="todo-app">
    <h1>Vue.js Todo App</h1>
    <input
      type="text"
      v-model="newTodo"
      @keyup.enter="addTodo"
      placeholder="Add a new todo"
    />
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <input type="checkbox" v-model="todo.isComplete" @change="updateTodo(todo)" />
        <span :class="{ completed: todo.isComplete }">{{ todo.name }}</span>
        <button @click="removeTodo(todo.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      newTodo: '',
      todos: [],
    };
  },
  methods: {
    async fetchTodos() {
      try {
        const response = await axios.get('http://localhost:5000/api/TodoItems');
        this.todos = response.data;
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    },
    async addTodo() {
      if (this.newTodo.trim()) {
        try {
          const response = await axios.post('http://localhost:5000/api/TodoItems', {
            name: this.newTodo,
            isComplete: false,
          });
          this.todos.push(response.data);
          this.newTodo = '';
        } catch (error) {
          console.error('Error adding todo:', error);
        }
      }
    },
    async updateTodo(todo) {
      try {
        await axios.put(`http://localhost:5000/api/TodoItems/${todo.id}`, todo);
      } catch (error) {
        console.error('Error updating todo:', error);
      }
    },
    async removeTodo(id) {
      try {
        await axios.delete(`http://localhost:5000/api/TodoItems/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    },
  },
  mounted() {
    this.fetchTodos();
  },
};
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}
</style>
