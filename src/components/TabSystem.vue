<template>
    <div class="tab-system">
      <div
        v-for="(tab, index) in tabs"
        :key="index"
        class="tab"
        :class="{ active: tab === activeTab }"
        @click="switchTab(tab)"
      >
        <span>{{ tab.name }}</span>
        <button @click.stop="closeTab(index)" class="close-btn">✖</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      tabs: Array,
      activeTab: Object,
    },
    methods: {
      switchTab(tab) {
        this.$emit("switchTab", tab);
      },
      closeTab(index) {
        this.$emit("closeTab", index);
      },
    },
  };
  </script>
  
  <style scoped>
  .tab-system {
    display: flex;
    background-color: #242629;
    border-radius: 8px 8px 0 0;
    padding: 0.5rem 0.5rem 0;
    overflow-x: auto;
    white-space: nowrap;
  }
  
  .tab {
    padding: 0.5rem 1rem;
    background-color: #16161a;
    color: #94a1b2;
    border-radius: 8px 8px 0 0;
    cursor: pointer;
    transition: background-color 0.3s ease;
    display: flex;
    align-items: center;
    margin-right: 0.25rem;
  }
  
  .tab:hover {
    background-color: #2cb67d;
    color: white;
  }
  
  .tab.active {
    background-color: #127cd3;
    color: white;
  }
  
  .close-btn {
    margin-left: 0.5rem;
    background: transparent;
    color: inherit;
    border: none;
    cursor: pointer;
    font-size: 0.8rem;
    padding: 0.1rem 0.3rem;
    border-radius: 50%;
    transition: background-color 0.3s ease;
  }
  
  .close-btn:hover {
    background-color: rgba(255, 255, 255, 0.2);
  }
  </style>