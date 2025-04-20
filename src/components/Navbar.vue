<template>
  <nav>
    <ul>
      <li><router-link to="/">Home</router-link></li>
      <li><router-link to="/about">About</router-link></li>
      <li><router-link to="/contact">Contact</router-link></li>
    </ul>
  </nav>
</template>

<script setup>
// This is a simple navigation bar with links
</script>

<style scoped>
nav {
  background-color: #333;
  padding: 3px;
}

ul {
  list-style-type: none;
  display: flex;
}

li {
  margin-right: 20px;
}

router-link {
  color: white;
  text-decoration: none;
}

router-link:hover {
  text-decoration: underline;
}
</style>
