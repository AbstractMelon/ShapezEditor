<template>
    <div class="dropdown">
      <button @click="toggleMenu">Settings ⯆</button>
      <ul v-if="showMenu">
        <li v-for="option in options" :key="option">{{ option }}</li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        showMenu: false,
        options: ["Option 1", "Option 2", "Option 3"],
      };
    },
    methods: {
      toggleMenu() {
        this.showMenu = !this.showMenu;
      },
    },
  };
  </script>
  
  <style scoped>
  .dropdown {
    position: relative;
    display: inline-block;
  }
  button {
    background-color: #303060;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    cursor: pointer;
  }
  ul {
    display: none;
    position: absolute;
    background-color: #202040;
    color: white;
    padding: 1rem;
    list-style-type: none;
    border-radius: 8px;
    margin: 0;
  }
  ul > li {
    margin: 0.5rem 0;
    cursor: pointer;
  }
  ul > li:hover {
    background-color: #404080;
  }
  </style>
  