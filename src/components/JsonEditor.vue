<template>
  <div class="json-editor">
    <div class="json-tree">
      <json-node
        v-for="(value, key) in jsonContent"
        :key="key"
        :nodeKey="key"
        :nodeValue="value"
        :depth="0"
        @updateValue="updateValue"
      />
    </div>
  </div>
</template>

<script>
import JsonNode from './JsonNode.vue';

export default {
  name: 'JsonEditor',
  components: {
    JsonNode
  },
  props: {
    jsonContent: {
      type: Object,
      required: true
    }
  },
  methods: {
    updateValue(path, value) {
      const updatedJson = JSON.parse(JSON.stringify(this.jsonContent));
      let current = updatedJson;
      const keys = path.split('.');
      const lastKey = keys.pop();

      for (const key of keys) {
        if (current[key] === undefined) {
          current[key] = {};
        }
        current = current[key];
      }

      current[lastKey] = value;
      this.$emit('updateJson', updatedJson);
    }
  }
}
</script>

<style scoped>
.json-editor {
  font-family: 'Courier New', monospace;
  padding: 1rem;
  background-color: #16161a;
  height: 100%;
  overflow-y: auto;
}

.json-tree {
  color: #94a1b2;
}
</style>