<template>
  <div id="app">
    <todoForm
      :todo=todo
      @updateTodo="updateTodo"
    >
    </todoForm>
  </div>
</template>

<script>
import todoForm from "./components/todoForm.vue";

export default {
  name: 'App',
  components: {
    todoForm
  },
  data() {
    return {
      todos: [],
      todo: {}
    }
  },
  methods: {
    updateTodo: function(obj) {
      this.todo = obj
      
      this.todos.push(this.todo)
      console.log(this.todos)

      this.todo = {}
    }
  }
}
</script>

<style lang="scss">
@import './assets/css/reset.css';

// #app {
//   font-family: Avenir, Helvetica, Arial, sans-serif;
//   -webkit-font-smoothing: antialiased;
//   -moz-osx-font-smoothing: grayscale;
//   text-align: center;
//   color: #2c3e50;
//   margin-top: 60px;
// }
</style>
