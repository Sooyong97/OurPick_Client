<template>
  <div id="app">
    <nav class="navbar">
      <div class="container">
        <router-link to="/" class="logo">OurPick</router-link>
        <div class="nav-links">
          <router-link to="/">홈</router-link>
          <router-link to="/about">소개</router-link>
          <router-link to="/contact">문의하기</router-link>
        </div>
      </div>
    </nav>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
/* Global styles */
html,
body,
#app {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  height: 100%; /* Ensure app takes full height */
  width: 100%; /* Ensure app takes full width */
}

*, *::before, *::after {
  box-sizing: inherit;
}


#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #333; /* Default text color for light theme */
  background-color: #f8f9fa; /* Light background */
}

.navbar {
  background-color: #fff; /* Light background for navbar */
  padding: 1rem 0;
  position: relative;
  z-index: 10;
  border-bottom: 1px solid #eee; /* Optional separator */
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 1rem; /* Keep horizontal padding for container */
  display: flex;
  align-items: center;
  /* Remove justify-content: justify-content: space-between; */
}

.logo {
  font-size: 1.5rem;
  font-weight: bold;
  color: #35495e; /* Darker logo color */
  text-decoration: none;
}

.nav-links {
  display: flex;
  gap: 2rem;
  margin-left: auto; /* Push links to the right */
}

.nav-links a {
  color: #555; /* Standard link color */
  text-decoration: none;
  font-weight: 500;
  transition: color 0.3s ease;
}

.nav-links a:hover,
.nav-links a.router-link-exact-active {
  color: #42b983; /* Highlight color on hover/active */
}
</style> 