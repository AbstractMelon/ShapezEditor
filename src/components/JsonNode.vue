<template>
    <div class="json-node" :style="{ marginLeft: `${depth * 20}px` }">
      <div class="node-content">
        <button v-if="isObject" @click="toggleExpanded" class="expand-btn">
          {{ expanded ? '▼' : '▶' }}
        </button>
        <span class="node-key">{{ nodeKey }}:</span>
        <template v-if="!isObject">
          <input
            v-if="typeof nodeValue === 'string'"
            :value="nodeValue"
            @input="updateValue($event.target.value)"
          />
          <input
            v-else-if="typeof nodeValue === 'number'"
            type="number"
            :value="nodeValue"
            @input="updateValue(Number($event.target.value))"
          />
          <select
            v-else-if="typeof nodeValue === 'boolean'"
            :value="nodeValue"
            @change="updateValue($event.target.value === 'true')"
          >
            <option value="true">true</option>
            <option value="false">false</option>
          </select>
        </template>
      </div>
      <div v-if="isObject && expanded" class="node-children">
        <json-node
          v-for="(value, key) in nodeValue"
          :key="key"
          :nodeKey="key"
          :nodeValue="value"
          :depth="depth + 1"
          @updateValue="updateChildValue"
        />
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'JsonNode',
    props: {
      nodeKey: {
        type: String,
        required: true
      },
      nodeValue: {
        required: true
      },
      depth: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        expanded: false
      }
    },
    computed: {
      isObject() {
        return typeof this.nodeValue === 'object' && this.nodeValue !== null;
      }
    },
    methods: {
      toggleExpanded() {
        this.expanded = !this.expanded;
      },
      updateValue(value) {
        this.$emit('updateValue', this.nodeKey, value);
      },
      updateChildValue(key, value) {
        this.$emit('updateValue', `${this.nodeKey}.${key}`, value);
      }
    }
  }
  </script>
  
  <style scoped>
  .json-node {
    margin-bottom: 0.25rem;
  }
  
  .node-content {
    display: flex;
    align-items: center;
  }
  
  .expand-btn {
    background: none;
    border: none;
    color: #94a1b2;
    cursor: pointer;
    font-size: 0.8rem;
    padding: 0 0.25rem;
  }
  
  .node-key {
    font-weight: bold;
    margin-right: 0.5rem;
  }
  
  input, select {
    background-color: #242629;
    color: #94a1b2;
    border: 1px solid #2cb67d;
    padding: 0.25rem;
    border-radius: 4px;
  }
  
  .node-children {
    margin-top: 0.25rem;
  }
  </style>