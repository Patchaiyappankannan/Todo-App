<template>
  <div class="card-container">
    <div class="card">
      <form @submit.prevent="login">
        <h2 class="card-title">Login</h2>
        <div v-if="errorMessage" class="error">{{ errorMessage }}</div>
        <div class="form-group">
          <input v-model="email" type="email" placeholder="Email" required class="form-control" />
        </div>
        <div class="form-group">
          <div class="password-wrapper">
            <input
              v-model="password"
              :type="isPasswordVisible ? 'text' : 'password'"
              placeholder="Password"
              required
              class="form-control"
            />
            <i
              class="password-toggle"
              @click="togglePasswordVisibility"
              :class="isPasswordVisible ? 'fa fa-eye-slash' : 'fa fa-eye'"
            ></i>
          </div>
        </div>
        <button type="submit" :disabled="isFormInvalid" class="login-button">Login</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      email: '',
      password: '',
      errorMessage: '',
      isPasswordVisible: false, 
    };
  },
  computed: {
    isEmailValid() {
      const emailPattern = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
      return emailPattern.test(this.email);
    },
    isFormInvalid() {
      return !this.email || !this.password || !this.isEmailValid;
    },
  },
  methods: {
    togglePasswordVisibility() {
      this.isPasswordVisible = !this.isPasswordVisible;
    },
    async login() {
      if (!this.isEmailValid) {
        this.errorMessage = 'Please enter a valid email address.';
        return;
      }
      if (!this.password) {
        this.errorMessage = 'Password is required.';
        return;
      }

      this.errorMessage = '';

      try {
        const { data } = await axios.get('http://localhost:3000/users', {
          params: { email: this.email, password: this.password },
        });
        if (data.length > 0) {
          localStorage.setItem('token', JSON.stringify(data[0]));
          this.$router.push('/dashboard');
        } else {
          this.errorMessage = 'Invalid credentials!';
        }
      } catch (error) {
        this.errorMessage = 'There was an error. Please try again later.';
      }
    },
  },
};
</script>

<style scoped>
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 10vh;
  background-color: #f4f4f9;
  padding: 20px;
}

.card {
  width: 100%;
  max-width: 400px;
  padding: 20px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.card-title {
  font-size: 1.5rem;
  margin-bottom: 20px;
  color: #333;
}

.error {
  color: red;
  font-size: 0.9rem;
  margin-bottom: 10px;
}

.form-group {
  margin-bottom: 15px;
}

.form-control {
  width: 100%;
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

.password-wrapper {
  position: relative;
}

.password-toggle {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  cursor: pointer;
  color: #007bff;
}

.password-toggle:hover {
  color: #0056b3;
}

.login-button {
  width: 100%;
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.login-button:hover {
  background-color: #0056b3;
}

.login-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
