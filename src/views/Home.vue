<template>
  <div class="home">
    <div class="auth-container">
      <div class="auth-box">
        <h1>OurPick</h1>
        <div class="auth-links">
          <router-link to="/signup" class="auth-button signup">회원가입</router-link>
          <router-link to="/login" class="auth-button login">로그인</router-link>
        </div>
        <input type="text" placeholder="무엇을 찾아볼까요?" class="search-input">
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home'
}
</script>

<style scoped>
.home {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: calc(100vh - 80px); /* Adjust based on navbar height */
  background-image: url('https://images.unsplash.com/photo-1522748906645-95d8adfd52e7?q=80&w=1920&auto=format&fit=crop'); /* Spring travel background */
  background-size: cover;
  background-position: center;
  padding: 2rem;
}

.auth-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.auth-box {
  background-color: rgba(255, 255, 255, 0.9); /* Semi-transparent white background */
  padding: 3rem;
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  text-align: center;
  width: 100%;
  max-width: 450px; /* Limit box width */
  border: 2px solid #add8e6; /* Light blue border as in the image */
}

.auth-box h1 {
  color: #35495e;
  margin-bottom: 2rem;
  font-size: 2.5rem;
  font-weight: bold;
}

.auth-links {
  background-color: #e0f2f7; /* Light blue background for links area */
  border: 1px solid #b0dde8; /* Slightly darker blue border */
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  border-radius: 8px;
}

.auth-button {
  display: block;
  padding: 0.8rem;
  margin: 0.5rem 0;
  text-decoration: none;
  border-radius: 5px;
  font-weight: 500;
  font-size: 1.1rem;
  transition: background-color 0.3s ease;
}

.auth-button.signup {
  background-color: #42b983;
  color: white;
}

.auth-button.login {
  background-color: #35495e;
  color: white;
}

.auth-button:hover {
  opacity: 0.9;
}

.search-input {
  width: calc(100% - 1.6rem); /* Adjust width considering padding */
  padding: 0.8rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1rem;
  margin-top: 1rem;
}
</style> 