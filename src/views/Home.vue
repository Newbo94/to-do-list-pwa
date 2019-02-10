<template>
  <div class="home">
    <div class="flex-wrapper-justify">
      <h1 class="main-heading--big">My to do list</h1>
      <span class="flex-wrapper-justify">
        <p class="sub-info--opacity">Items</p>
        <h2 class="main-heading--medium">{{remaning}}</h2>
      </span>
    </div>
    <div class="to-do-card">
      <input
        v-model="newToDo"
        @keyup.enter="addTodo"
        type="text"
        class="to-do-card--input"
        placeholder="Add new items.."
      >

      <div class="todo-item" v-for="(todo, index) in todos" :key="todo.id">
       <div>
       <input type="checkbox" v-model="todo.completed">
        <div
          :class="{completed : todo.completed}"
          v-if="!todo.editing"
          @dblclick="editToDo(todo)"
          class="todo-item-label"
        >{{todo.title}}
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
        >
</div>
        <div class="remove-item" @click="removeToDo(index)">&times;</div>
      </div>
    </div>
    <button class="main-button--mainc">Edit</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newToDo: "",
      idForTodo: 3,
      beforeEditCache: "",
      todos: [
        {
          id: 1,
          title: "Finish vue screencast",
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: "Finish vue 2",
          completed: false,
          editing: false
        }
      ]
    };
  },

  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },

  computed: {
    remaning() {
      return this.todos.filter(todo => !todo.completed).length;
    }
  },

  methods: {
    addTodo() {
      if (this.newToDo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newToDo,
        completed: false
      });

      (this.newToDo = ""), this.idForTodo++;
    },

    removeToDo(index) {
      this.todos.splice(index, 1);
    },

    editToDo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    doneEdit(todo) {
      todo.editing = false;
      if (todo.title.trim() === '') {
        todo.title = this.beforeEditCache;
      }
    },
    cancelEdit(todo) {
      todo.editing = false;
      todo.title = this.beforeEditCache;
    }
  }
};
</script>
