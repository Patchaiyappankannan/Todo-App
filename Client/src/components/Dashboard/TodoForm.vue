<template>
  <div class="todo-form-container">
    <form @submit.prevent="submitTodo" class="todo-form">
      <h3 class="form-title">Add Todo</h3>
      <div class="form-group">
        <label for="title">Title:</label>
        <input 
          v-model="form.title" 
          id="title" 
          type="text" 
          placeholder="Enter the title of your task" 
          class="form-control" 
          required 
        />
      </div>
      <div class="form-group">
        <label for="description">Description:</label>
        <textarea 
          v-model="form.description" 
          id="description" 
          placeholder="Provide a brief description of the task" 
          class="form-control"
        ></textarea>
      </div>
      <div class="form-group">
        <label>Priority:</label>
        <div class="priority-options">
          <label><input v-model="form.priority" type="radio" value="High" /> High</label>
          <label><input v-model="form.priority" type="radio" value="Medium" /> Medium</label>
          <label><input v-model="form.priority" type="radio" value="Low" /> Low</label>
        </div>
      </div>
      <div class="form-group">
        <label class="checkbox-container">
          <input v-model="form.completed" type="checkbox" /> Mark as completed
        </label>
      </div>
      <button type="submit" class="submit-button">Add Todo</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      form: {
        title: '',
        description: '',
        priority: 'Medium',
        completed: false,
      },
      todos: [],
    };
  },
  methods: {
    async submitTodo() {
      if (!this.form.title) {
        alert('Title is required!');
        return;
      }

      const todoData = {
        title: this.form.title,
        description: this.form.description,
        priority: this.form.priority,
        completed: this.form.completed,
      };

      try {
        const response = await axios.post('http://localhost:3000/todos', todoData);

        this.form = { title: '', description: '', priority: 'Medium', completed: false };

        this.todos.push(response.data);

        this.$emit('addTodo', response.data);

        alert('Todo added successfully!');

        this.fetchTodos();
      } catch (error) {
        console.error('Error adding todo:', error);
        alert('There was an error adding your todo.');
      }
    },
    async fetchTodos() {
      try {
        const response = await axios.get('http://localhost:3000/todos');
        this.todos = response.data;
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    },
  },
  created() {
    this.fetchTodos();
  },
};
</script>

<style scoped>
.todo-form-container {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  background: #f5f5f5;
  min-height: 10vh;
}

.todo-form {
  width: 100%;
  max-width: 500px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.form-title {
  font-size: 1.5rem;
  margin-bottom: 20px;
  text-align: center;
  color: #333;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: 600;
  color: #555;
}

.form-control {
  width: 100%;
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  outline: none;
  transition: border-color 0.3s ease;
}

.form-control:focus {
  border-color: #007bff;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

.form-control::placeholder {
  color: #888;
  font-style: italic;
}

.priority-options label {
  margin-right: 10px;
  font-weight: 500;
  color: #333;
}

.checkbox-container {
  font-weight: 500;
  color: #333;
}

.submit-button {
  width: 100%;
  padding: 10px 15px;
  font-size: 1rem;
  color: white;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.submit-button:hover {
  background-color: #0056b3;
}

.todo-form-container {
  background: #f5f5f5;
  min-height: 100vh;
}

.todo-form {
  background: #fff;
  padding: 25px;
  border-radius: 8px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
}

.submit-button {
  background-color: #007bff;
}

.submit-button:hover {
  background-color: #0056b3;
}
</style>
