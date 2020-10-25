<template>
  <div>
    <div v-for="item in tree" v-bind:key="item.id" class="tree">
      <div class="name">
        <span v-on:click="selectDoc(item)">{{ item.name }}</span>
        <span class="add" v-on:click="newDoc(item.id)">+</span>
      </div>
      <div class="childs">
        <Tree
          v-bind:tree="item.childs"
          v-on:select="selectDoc"
          v-on:new="newDoc"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Tree',
  props: {
    tree: Array
  },
  methods: {
    selectDoc (doc) {
      this.$emit('select', doc)
    },
    newDoc (parent_id) {
      this.$emit('new', parent_id)
    }
  }
}
</script>

<style scoped>
.name {
  margin-bottom: 4px;
}
.add {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 16px;
  height: 16px;
  margin-left: 10px;
  border: 1px solid #ddd;
  cursor: pointer;
}
.childs {
  margin-left: 10px;
}
</style>
