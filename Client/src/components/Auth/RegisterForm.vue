<template>
  <div class="register-container">
    <form @submit.prevent="register" class="register-form">
      <h2 class="form-title">Register</h2>

      <div v-if="errorMessage" class="error">{{ errorMessage }}</div>

      <div class="form-group" :class="{ 'has-error': !isEmailValid && email }">
        <input v-model="email" type="email" placeholder="Email" required class="form-control" />
      </div>
      <div class="form-group" :class="{ 'has-error': !isPhoneValid && phone }">
        <input v-model="phone" type="tel" placeholder="Phone (10 digits)" required class="form-control" />
      </div>
      <div class="form-group">
        <input v-model="name" type="text" placeholder="Name" required class="form-control" />
      </div>
      <div class="form-group" :class="{ 'has-error': !isPasswordValid && password }">
        <div class="password-container">
          <input 
            v-model="password" 
            :type="passwordVisible ? 'text' : 'password'" 
            placeholder="Password (at least 6 characters)" 
            required 
            class="form-control" 
          />
          <span @click="togglePasswordVisibility" class="password-icon">
            <i :class="passwordVisible ? 'fas fa-eye-slash' : 'fas fa-eye'"></i>
          </span>
        </div>
      </div>

      <button type="submit" :disabled="isFormInvalid" class="submit-button">Register</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      name: "",
      email: "",
      password: "",
      phone: "",
      errorMessage: "",
      passwordVisible: false, 
    };
  },
  computed: {
    isFormInvalid() {
      return (
        !this.name ||
        !this.email ||
        !this.password ||
        !this.phone ||
        !this.isEmailValid ||
        !this.isPhoneValid ||
        !this.isPasswordValid
      );
    },
    isEmailValid() {
      const emailPattern = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
      return emailPattern.test(this.email.trim());
    },
    isPhoneValid() {
      const phonePattern = /^[0-9]{10}$/;
      return phonePattern.test(this.phone.trim());
    },
    isPasswordValid() {
      return this.password.length >= 6;
    },
  },
  methods: {
    togglePasswordVisibility() {
      this.passwordVisible = !this.passwordVisible; 
    },
    async register() {
      if (this.isFormInvalid) {
        this.errorMessage = "Please ensure all fields are correctly filled.";
        return;
      }

      this.errorMessage = "";

      try {
        await axios.post("http://localhost:3000/users", {
          name: this.name.trim(),
          email: this.email.trim(),
          password: this.password,
          phone: this.phone.trim(),
        });
        alert("Registration successful!");
        this.$router.push('/'); 
      } catch (error) {
        this.errorMessage = "There was an error during registration. Please try again.";
      }
    },
  },
};
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 55vh;
  padding: 20px;
}

.register-form {
  width: 100%;
  max-width: 400px;
  padding: 20px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.form-title {
  font-size: 1.8rem;
  margin-bottom: 20px;
  color: #007bff;
}

.form-group {
  margin-bottom: 15px;
}

.form-control {
  width: 100%;
  padding: 12px 15px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
  outline: none;
  transition: border-color 0.3s ease;
}

.form-control:focus {
  border-color: #007bff;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

.has-error .form-control {
  border-color: red;
  box-shadow: 0 0 5px rgba(255, 0, 0, 0.5);
}

.password-container {
  position: relative;
}

.password-icon {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  cursor: pointer;
}

.submit-button {
  width: 100%;
  padding: 12px;
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

.submit-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.error {
  color: red;
  font-size: 0.9rem;
  margin-bottom: 10px;
  text-align: left;
}
</style>
