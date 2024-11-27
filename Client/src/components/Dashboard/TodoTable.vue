<template>
  <div>
    <h3>Todo List</h3>
    <table class="todos-table">
      <thead>
        <tr>
          <th>Title</th>
          <th>Description</th>
          <th>Priority</th>
          <th>Completed</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="todo in todos" :key="todo.id">
          <td>{{ todo.title }}</td>
          <td>{{ todo.description }}</td>
          <td>{{ todo.priority }}</td>
          <td>
            <span :class="todo.completed ? 'completed' : 'incomplete'">
              {{ todo.completed ? 'Yes' : 'No' }}
            </span>
          </td>
          <td>
            <button @click="editTodo(todo)" class="edit-btn">Edit</button>
            <button @click="deleteTodo(todo.id)" class="delete-btn">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit Todo Form Modal -->
    <div v-if="isEditing" class="edit-modal">
      <div class="modal-content">
        <h3>Edit Todo</h3>
        <form @submit.prevent="submitEditTodo">
          <div class="form-group">
            <label for="edit-title">Title:</label>
            <input 
              v-model="editForm.title" 
              id="edit-title" 
              type="text" 
              required
              placeholder="Enter todo title"
            />
          </div>
          <div class="form-group">
            <label for="edit-description">Description:</label>
            <textarea 
              v-model="editForm.description" 
              id="edit-description"
              placeholder="Enter description"
            ></textarea>
          </div>
          <div class="form-group">
            <label>Priority:</label>
            <div class="priority-options">
              <label><input v-model="editForm.priority" type="radio" value="High" /> High</label>
              <label><input v-model="editForm.priority" type="radio" value="Medium" /> Medium</label>
              <label><input v-model="editForm.priority" type="radio" value="Low" /> Low</label>
            </div>
          </div>
          <div class="form-group">
            <label>
              <input v-model="editForm.completed" type="checkbox" /> Mark as completed
            </label>
          </div>
          <div class="form-actions">
            <button type="submit" class="submit-button">Update Todo</button>
            <button type="button" @click="closeEditModal" class="cancel-button">Cancel</button>
          </div>
        </form>
      </div>
    </div>
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
      isEditing: false, 
      editForm: {
        id: null,
        title: '',
        description: '',
        priority: 'Medium',
        completed: false,
      }
    };
  },
  created() {
    this.fetchTodos();  
  },
  methods: {
    fetchTodos() {
      axios.get('http://localhost:3000/todos')
        .then(response => {
          this.todos = response.data;
        })
        .catch(error => {
          console.error('Error fetching todos:', error);
        });
    },

    submitTodo() {
      if (!this.form.title) {
        alert('Title is required!');
        return;
      }
      
      axios.post('http://localhost:3000/todos', this.form)
        .then(response => {
          console.log('Todo Added:', response.data);
          this.todos.push(response.data);
          this.form = { title: '', description: '', priority: 'Medium', completed: false };
        })
        .catch(error => {
          console.error('Error adding todo:', error);
        });
    },

    deleteTodo(id) {
      axios.delete(`http://localhost:3000/todos/${id}`)
        .then(response => {
          console.log('Todo Deleted:', response.data);
          this.todos = this.todos.filter(todo => todo.id !== id);  
        })
        .catch(error => {
          console.error('Error deleting todo:', error);
        });
    },

    editTodo(todo) {
      this.isEditing = true;
      this.editForm = { ...todo };
    },

    submitEditTodo() {
      axios.put(`http://localhost:3000/todos/${this.editForm.id}`, this.editForm)
        .then(response => {
          console.log('Todo Updated:', response.data);
          const index = this.todos.findIndex(todo => todo.id === this.editForm.id);
          if (index !== -1) {
            this.todos[index] = response.data; 
          }
          this.closeEditModal();  
        })
        .catch(error => {
          console.error('Error updating todo:', error);
        });
    },

    closeEditModal() {
      this.isEditing = false;
      this.editForm = { id: null, title: '', description: '', priority: 'Medium', completed: false }; 
    }
  }
};
</script>

<style scoped>
.todos-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: #fff;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.todos-table th,
.todos-table td {
  padding: 12px;
  text-align: left;
  border: 1px solid #ddd;
}

.todos-table th {
  background-color: #007bff;
  color: white;
}

.todos-table td {
  background-color: #f9f9f9;
}

.todos-table tr:nth-child(even) {
  background-color: #f1f1f1;
}

.todos-table tr:hover {
  background-color: #f4f4f4;
}

.todos-table .completed {
  color: green;
  font-weight: bold;
}

.todos-table .incomplete {
  color: red;
  font-weight: bold;
}

.edit-btn, .delete-btn {
  padding: 6px 12px;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}

.edit-btn {
  background-color: #f39c12;
}

.edit-btn:hover {
  background-color: #e67e22;
}

.delete-btn {
  background-color: #e74c3c;
}

.delete-btn:hover {
  background-color: #c0392b;
  margin-right: 0;
}

.edit-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 15px;
}

.form-actions {
  display: flex;
  justify-content: space-between;
}

.submit-button {
  padding: 10px 15px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.submit-button:hover {
  background-color: #0056b3;
}

.cancel-button {
  padding: 10px 15px;
  background-color: #ccc;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.cancel-button:hover {
  background-color: #bbb;
}
</style>
