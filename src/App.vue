<template>
  <div class="app">
    <div class="header">
      <h1>Shapez 2 Save Editor</h1>
      <div class="file-upload">
        <label for="file-upload" class="custom-file-upload">
          Choose SPZ2 File
        </label>
        <input id="file-upload" type="file" @change="handleFileUpload" accept=".spz2" />
        <button @click="downloadEditedFile" v-if="jsonFiles.length">Download Edited File</button>
      </div>
    </div>

    <!-- Placeholder when no file is loaded -->
    <div v-if="!jsonFiles.length" class="placeholder">
      <h2>Please select a file to begin editing.</h2>
    </div>

    <div class="main-content" v-if="jsonFiles.length">
      <div class="sidebar">
        <file-tree :files="jsonFiles" :selectedFile="activeTab" @selectFile="openFileInTab" />
      </div>
      
      <div class="editor-container">
        <tab-system :tabs="tabs" :activeTab="activeTab" @switchTab="switchTab" @closeTab="closeTab" />
        <json-editor 
          v-if="activeTab" 
          :jsonContent="activeTab.content" 
          @updateJson="updateJson(activeTab.index, $event)"
        />
      </div>
    </div>
  </div>
</template>

<script>
import JSZip from "jszip";
import TabSystem from "./components/TabSystem.vue";
import FileTree from "./components/FileTree.vue";
import JsonEditor from "./components/JsonEditor.vue";

export default {
  components: { TabSystem, FileTree, JsonEditor },
  data() {
    return {
      jsonFiles: [],
      tabs: [],
      activeTab: null,
      zip: null,
    };
  },
  methods: {
      async handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        this.zip = new JSZip();
        await this.zip.loadAsync(file);
        this.extractJsonFiles();
      }
    },
    async extractJsonFiles() {
      this.jsonFiles = [];
      this.tabs = [];
      this.activeTab = null;

      // Iterate through the zip file and store all files (JSON and others)
      this.zip.forEach(async (relativePath, file) => {
        if (file.name.endsWith(".json")) {
          const content = await file.async("string");
          this.jsonFiles.push({ name: relativePath, content: JSON.parse(content) });
        }
      });
    },
    async downloadEditedFile() {
      const newZip = new JSZip();

      // Add all original files back to the new zip (including directories)
      await Promise.all(
        Object.keys(this.zip.files).map(async (relativePath) => {
          const file = this.zip.files[relativePath];
          if (!file.dir) {
            if (!relativePath.endsWith(".json")) {
              // Add non-JSON files unchanged
              const content = await file.async("blob");
              newZip.file(relativePath, content);
            }
          }
        })
      );

      // Add the edited JSON files back to the zip
      this.jsonFiles.forEach((jsonFile) => {
        newZip.file(jsonFile.name, JSON.stringify(jsonFile.content, null, 2));
      });

      // Generate the zip file and trigger download
      const content = await newZip.generateAsync({ type: "blob" });
      const link = document.createElement("a");
      link.href = URL.createObjectURL(content);
      link.download = "edited_save.spz2";
      link.click();
    },
    openFileInTab(file) {
      const existingTab = this.tabs.find((tab) => tab.name === file.name);
      if (!existingTab) {
        const newTab = { name: file.name, content: file.content, index: this.tabs.length };
        this.tabs.push(newTab);
        this.activeTab = newTab;
      } else {
        this.activeTab = existingTab;
      }
    },
    switchTab(tab) {
      this.activeTab = tab;
    },
    closeTab(index) {
      this.tabs.splice(index, 1);
      if (this.tabs.length) {
        this.activeTab = this.tabs[Math.min(index, this.tabs.length - 1)];
      } else {
        this.activeTab = null;
      }
    },
    updateJson(index, updatedContent) {
      this.tabs[index].content = updatedContent;
      // Update the corresponding file in jsonFiles
      const fileIndex = this.jsonFiles.findIndex((file) => file.name === this.tabs[index].name);
      if (fileIndex !== -1) {
        this.jsonFiles[fileIndex].content = updatedContent;
      }
    },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
  background-color: #242424;
}

.app {
  display: flex;
  flex-direction: column;
  background-color: #242424;
  color: #f0f0f0;
  height: 100vh;
  font-family: 'Arial', sans-serif;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background-color: #16161a;
}

h1 {
  margin: 0;
  color: #127cd3;
}

.placeholder {
  text-align: center;
  margin-top: 2rem;
  color: #94a1b2;
}

.file-upload {
  display: flex;
  align-items: center;
}

.custom-file-upload {
  display: inline-block;
  padding: 0.5rem 1rem;
  cursor: pointer;
  background-color: #127cd3;
  color: white;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.custom-file-upload:hover {
  background-color: #0d5fa2;
}

button {
  display: inline-block;
  padding: 0.5rem 1rem;
  cursor: pointer;
  background-color: #127cd3;
  color: white;
  border-radius: 5px;
  transition: background-color 0.3s ease;
  margin-left: 1rem;
}

button:hover {
  background-color: #0d5fa2;
}

input[type="file"] {
  display: none;
}

.main-content {
  display: flex;
  flex: 1;
  overflow: hidden;
}

.sidebar {
  width: 250px;
  overflow-y: auto;
  background-color: #242629;
}

.editor-container {
  flex: 1;
  display: flex;
  flex-direction: column;
  background-color: #16161a;
}
</style>