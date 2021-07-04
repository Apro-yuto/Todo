<template>
  <div id="app">
    <todoForm
      :todo=todo
      @updateTodo="updateTodo"
    >
    </todoForm>
    <displayTodo
      :todos="todos"
      @removeTodo="removeTodo"
      @removeDetailTodo="removeDetailTodo"
      @pushDetail="pushDetail"
    >
    </displayTodo>
  </div>
</template>

<script>
import todoForm from "./components/todoForm.vue";
import displayTodo from "./components/displayTodo.vue";

var STORAGE_KEY = 'yuto-todoNew'
var todoStorage = {
  fetch: function () {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    todos.forEach(function (todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

export default {
  name: 'App',
  components: {
    todoForm,
    displayTodo
  },
  data() {
    return {
      todos: todoStorage.fetch(),
      todo: {}
    }
  },
  methods: {
    updateTodo: function(obj) {
      this.todo = obj
      this.todo.id = this.todos.length

      this.todos.push(this.todo)

      this.todo = {}
    },
    removeTodo: function(i) {
      this.todos.splice(i, 1)
    },
    pushDetail:function(name, date, i) {
      let resultObj = {
        name: name,
        date: date
      }
      this.todos[i].detailArr.push(resultObj)
    },
    removeDetailTodo: function(i, detailIndex) {
      this.todos[i].detailArr.splice(detailIndex, 1)
    }
  },
  watch: {
    todos: {
      handler: function(newitem) {
        todoStorage.save(newitem)
      },
      deep: true
    }
  }
}
</script>

<style lang="scss">
@import './assets/css/reset.css';
#app {
  display: flex;
  align-items: flex-start;
  width: 100%;
  padding: 50px 50px 50px 0;
}
</style>
