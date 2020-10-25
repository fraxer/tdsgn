<template>
  <div class="main">
    <div class="aside">
      <button v-on:click="newDoc(0)" class="add">Новый документ +</button>
      <Tree
        v-bind:tree="tree"
        v-on:select="selectDoc"
        v-on:new="newDoc"
      />
    </div>
    <div class="content">
      <input type="text" v-model="doc.name" class="docname" placeholder="Название документа">

      <div v-if="Object.keys(doc.params).length" class="params">
        <div class="params__title">Атрибуты</div>
        <div v-for="(param, key) in doc.params" v-bind:key="key" class="param">{{ key }}: {{ param }}</div>
      </div>

      <div class="form">
        <input type="text" v-model="paramKey" placeholder="атрибут">
        <input type="text" v-model="paramValue" placeholder="значение">
        <button v-on:click="addParam">Добавить атрибут</button>
      </div>

      <br>
      <br>

      <button v-if="doc.id" v-on:click="saveDoc">Сохранить документ</button>
      <button v-else v-on:click="createDoc">Создать документ</button>
    </div>
  </div>
</template>

<script>
import Tree from './components/Tree.vue'

export default {
  name: 'App',
  data () {
    return {
      docList: [
        {
          id: 1,
          parent_id: 0,
          name: 'Документ 1',
          params: {
            'Параметр 1': 'текст 1',
            'Параметр 2': 'текст 2'
          }
        },
        {
          id: 2,
          parent_id: 0,
          name: 'Документ 2',
          params: {
            'Параметр 11': 'текст 1',
            'Параметр 22': 'текст 2'
          }
        },
        {
          id: 3,
          parent_id: 1,
          name: 'Документ 3',
          params: {}
        },
        {
          id: 4,
          parent_id: 1,
          name: 'Документ 4',
          params: {
            'Параметр 4': 'текст 1'
          }
        },
        {
          id: 5,
          parent_id: 3,
          name: 'Документ 5',
          params: {
            'Параметр 5': 'текст 1'
          }
        }
      ],
      doc: {
        id: 0,
        parent_id: 0,
        name: '',
        params: {}
      },
      paramKey: '',
      paramValue: ''
    }
  },
  computed: {
    lastId () {
      let id = 0

      for (let doc of this.docList) {
        if (doc.id > id) {
          id = doc.id
        }
      }

      return ++id
    },
    tree () {
      let tree = []
      let link = {
        0: tree
      }

      for (let doc of this.docList) {
        let item = Object.assign({}, doc)
        item.childs = []

        link[doc.parent_id].push(item)

        link[doc.id] = item.childs
      }

      return tree
    }
  },
  created () {
  },
  methods: {
    selectDoc (doc) {
      for (let prop in this.doc) {
        let value = doc[prop]
        if (typeof value === 'object') {
          value = Object.assign({}, value)
        }
        this.doc[prop] = value
      }

      this.clearInputParams()
    },
    addParam () {
      if (!this.paramKey.length) {
        alert('атрибут не введен')
        return
      }
      if (!this.paramValue.length) {
        alert('значение не введено')
        return
      }
      this.doc.params[this.paramKey.trim()] = this.paramValue.trim()
      this.clearInputParams()
    },
    clearInputParams () {
      this.paramKey = ''
      this.paramValue = ''
    },
    newDoc (parent_id) {
      this.doc.id = 0
      this.doc.parent_id = parent_id
      this.doc.name = ''
      this.doc.params = {}

      this.clearInputParams()

      this.$el.querySelector('.docname').focus()
    },
    saveDoc () {
      for (let doc of this.docList) {
        if (doc.id === this.doc.id) {
          for (let prop in doc) {
            doc[prop] = this.doc[prop]
          }
          break
        }
      }

      this.clearInputParams()
    },
    createDoc () {
      if (!this.doc.name.length) {
        alert('название документа не введено')
        return
      }
      this.doc.id = this.lastId
      this.docList.push(Object.assign({}, this.doc))
      this.newDoc(0)
    },
  },
  components: {
    Tree
  }
}
</script>

<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}

.main {
  display: flex;
  align-items: stretch;
}

.aside {
  min-width: 200px;
  padding: 10px;
  border-right: 1px solid #ddd;
}

.content {
  padding: 10px;
}

.add {
  margin-bottom: 4px;
  cursor: pointer;
}

.docname {
  display: block;
  width: 400px;
  height: 28px;
  margin-bottom: 20px;
  border: 1px solid #ddd;
}

.params {
  margin-bottom: 20px;
}

.params__title {
  margin-bottom: 4px;
}

.param {
  margin-bottom: 4px;
  font-size: 12px;
}

.form {
  display: flex;
}
</style>
