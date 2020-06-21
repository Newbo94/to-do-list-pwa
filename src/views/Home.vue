<template>
  <div class="home">
    <div class="flex-wrapper-justify">
      <h1 class="main-heading--big">My to do list</h1>
      <span class="flex-wrapper-justify">
        <p class="sub-info--opacity">Itemsss</p>
        <h2 class="main-heading--medium">{{ remaning }}</h2>
      </span>
    </div>
    <div class="to-do-card">
      <input
        v-model="newToDo"
        @keyup.enter="addTodo"
        type="text"
        class="to-do-card--input"
        placeholder="Add new items.."
      />

      <div class="todo-item" v-for="(todo, index) in todos" :key="todo.id">
        <div>
          <input type="checkbox" v-model="todo.completed" />
          <div
            :class="{ completed: todo.completed }"
            v-if="!todo.editing"
            @dblclick="editToDo(todo)"
            class="todo-item-label"
          >
            {{ todo.title }}
          </div>
          <input
            v-else
            v-focus
            class="todo-item-edit"
            type="text"
            v-model="todo.title"
            @blur="doneEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @keyup.esc="cancelEdit(todo)"
          />
        </div>
        <div class="remove-item" @click="removeToDo(index)">&times;</div>
      </div>
    </div>
    <button class="main-button--mainc" @click="addIndexDb">Edit</button>
    <button class="" @click="display">Display items</button>
    <div v-for="(item, index) in dbItems" :key="index">
      <h1>{{ item }}</h1>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      newToDo: '',
      idForTodo: 3,
      beforeEditCache: '',
      dbItems: [{ id: 2, name: 'not index' }],
      todos: [
        {
          id: 1,
          title: 'Finish vue screencast',
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: 'Finish vue 2',
          completed: false,
          editing: false
        }
      ]
    }
  },

  created () {
    const reqDb = indexedDB.open('test-db', 1)
    let db
    reqDb.onupgradeneeded = (e) => {
      db = e.target.result
      if (!db.objectStoreNames.contains('db-list')) {
        db.createObjectStore('db-list', { keyPath: 'id' })
      }
    }

    reqDb.onsuccess = (e) => {
      db = e.target.result
      if (!db.objectStoreNames.contains('db-list')) {
        db.createObjectStore('db-list', { keyPath: 'id' })
      }
    }
  },

  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },

  computed: {
    remaning () {
      return this.todos.filter((todo) => !todo.completed).length
    }
  },

  methods: {
    addTodo () {
      if (this.newToDo.trim().length === 0) {
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newToDo,
        completed: false
      });

      // eslint-disable-next-line no-unused-expressions
      (this.newToDo = ''), this.idForTodo++
    },

    removeToDo (index) {
      this.todos.splice(index, 1)
    },

    editToDo (todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit (todo) {
      todo.editing = false
      if (todo.title.trim() === '') {
        todo.title = this.beforeEditCache
      }
    },
    cancelEdit (todo) {
      todo.editing = false
      todo.title = this.beforeEditCache
    },
    display () {
      const req = indexedDB.open('test-db')
      req.onsuccess = (e) => {
        const db = e.target.result
        const transaction = db.transaction('db-list', 'readwrite')
        const store = transaction.objectStore('db-list')
        const item = store.get(1)
        item.onsuccess = (e) => {
          this.dbItems.push(e.target.result)
        }
      }
    },

    addIndexDb () {
      const req = indexedDB.open('test-db')
      req.onsuccess = (e) => {
        const db = e.target.result
        const transaction = db.transaction('db-list', 'readwrite')
        const store = transaction.objectStore('db-list')
        store.put({ id: 1, name: 'index dude' })
      }
    }
  }
}
</script>
