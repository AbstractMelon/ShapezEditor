<template>
    <div class="file-tree">
      <h3>Files</h3>
      <ul>
        <li v-for="file in files" :key="file.name" @click="selectFile(file)" class="file-item">
          <span class="file-icon">📄</span>
          <span class="file-name">{{ file.name }}</span>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      files: Array,
    },
    methods: {
      selectFile(file) {
        this.$emit("selectFile", file);
      },
    },
  };
  </script>
  
  <style scoped>
  .file-tree {
    width: 250px;
    background-color: #242629;
    padding: 1rem;
    border-radius: 8px;
    color: #94a1b2;
  }
  
  h3 {
    margin-top: 0;
    margin-bottom: 1rem;
    color: #fffffe;
  }
  
  ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }
  
  .file-item {
    display: flex;
    align-items: center;
    padding: 0.5rem;
    cursor: pointer;
    transition: background-color 0.3s ease;
    border-radius: 4px;
  }
  
  .file-item:hover {
    background-color: #16161a;
  }
  
  .file-icon {
    margin-right: 0.5rem;
  }
  
  .file-name {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  </style>