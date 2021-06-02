<template>
  <div class="todoForm">
    <form class="todo_form">

      <div class="todo_name">
        <input
        v-model="todos.todoName"
        type="text"
        name="todoName"
        placeholder="TODO"
        class="todo_name-input">
      </div>

      <div class="todo_select">
        <label for="important">重要度</label>
        <div class="todo_select-body">
        <select 
          v-model="todos.important"
          name="important" 
          class="todo_select-area">
            <option value="default" selected>-</option>
            <option value="1">高</option>
            <option value="0">低</option>
        </select>
        </div>
      </div>

      <div class="todo_select">
        <label for="emergency">緊急度</label>
        <div class="todo_select-body">
          <select 
            v-model="todos.emargency"
            name="emergency" 
            class="todo_select-area"
          >
            <option value="default" selected>-</option>
            <option value="1">高</option>
            <option value="0">低</option>
          </select>
        </div>
      </div>

      <input 
        v-model="todos.todoDate"
        type="datetime-local" 
        class="todo_date"
      >

      <button 
        @click="updateTodo($event)"
        class="todo_submit"
      >
      登録
      </button>
    </form>
    <template v-if="error.existError === true">
      <div class="error">
        <p v-for="item in error.errorArr" :key="item.id">
          {{item}}
        </p>
      </div>
    </template>
    <button 
      @click="isDetailopen"
      class="moreBtn"
    >
    詳しく書く
    </button>
      <template
        v-if="detailOpen === true"
        class="todo_detail"
      >
        <li 
          v-for="item in todos.detailArr"
          :key="item.id"
          class="todo_detail-item pipe"
        >
        <div class="todo_detail-contents">
          <p>・{{item.name}}</p>
          <input 
            v-model="item.date"
            type="datetime-local"
          >
        </div>
        </li>
      <div class="todo_detail-add pipe">
        <input
          v-model="detailName"
          placeholder="loadmap"
          type="text">
        <input
          v-model="detailDate"
          type="datetime-local"
          class="todo_detail-date"
        >
        <button
          @click="updateDetail($event)"
        >追加</button>
      </div>
      <template v-if="error.existDetailError === true ">
        <ul class="todo_detail-err">
          <li
            v-for="item in error.detailErrorArr"
            :key="item.id"
          >
          {{ item }}
          </li>
        </ul>
      </template>
      </template>
  </div>
</template>

<script>
export default {
  name: 'todo-Form',
  props: {
    todo: Object,
  },
  data() {
    return {
      detailOpen: false,
      detailDate: '',
      detailName: '',
      todos: {
        todoName: '',
        important: '',
        emargency: '',
        todoDate: '',
        detailArr: new Array(),
      },
      detailObj: {
        name: '',
        date: ''
      },
      error: {
        existError: false,
        errorArr: [],
        existDetailError: false,
        detailErrorArr: []
      }
    }
  },
  methods: {
    isDetailopen: function() {
      if(this.detailOpen === true) {
        this.detailOpen = false
      } else if(this.detailOpen === false) {
        this.detailOpen = true
      }
    },
    updateTodo: function(e) {
      e.preventDefault()

      this.error.errorArr = new Array()
      this.invalidTodo()

      if(this.error.existError === true) return false
      console.log(this.todos)
      this.$emit('updateTodo', this.todos)

      this.$nextTick(function() {
        this.$set(this.todos, 'todoName' , "")
        this.$set(this.todos, 'important' , "")
        this.$set(this.todos, 'emargency' , "")
        this.$set(this.todos, 'todoDate' , "")
        this.$set(this.todos, 'detailArr' , [])
      })
    },
    updateDetail: function(e) {
      e.preventDefault()

      this.error.detailErrorArr = new Array()
      this.invalidDetail()

      if(this.error.existDetailError === true) return false
      console.log(this.detailObj)
      this.detailObj.name = this.detailName
      this.detailObj.date = this.detailDate
      this.todos.detailArr.push(this.detailObj)

      this.detailObj = {}
      this.detailName = ''
      this.detailDate = ''
    },
    invalidTodo: function() {
      let todoName = this.todos.todoName
      let important = this.todos.important
      let emargency = this.todos.emargency
      let todoDate = this.todos.todoDate

      if(todoName.length == 0) {
        this.error.existError = true
        this.error.errorArr.push('項目名を入力してください。')
      }
      if(important === 'default' || important === '') {
        this.error.existError = true
        this.error.errorArr.push('重要度を選択してください。')
      }
      if(emargency === 'default' || emargency === '') {
        this.error.existError = true
        this.error.errorArr.push('緊急度を選択してください。')
      }
      if(todoDate === '') {
        this.error.existError = true
        this.error.errorArr.push('期日を選択してください。')
      }
    },
    invalidDetail: function() {
      let detailName = this.detailName
      let detailDate = this.detailDate

      if(detailName.length === 0) {
        this.error.existDetailError = true
        this.error.detailErrorArr.push('項目名を入力してください。')
      }
      if(detailDate.length === 0) {
        this.error.existDetailError = true
        this.error.detailErrorArr.push('期日を選択してください。')
      }
    }
  },
  updated: function() {
    console.log(this.todos, this.detailObj)
  },
}
</script>

<style lang="scss">

%formBtn {
  font-size: 14px;
  display: inline-block;
  border: 1px solid #000;
  padding: 4px 16px;
  transition: .1s;
  &:hover {
    border: 3px dotted #000;
  }
}

%date {
  width: 260px;
  font-size: 14px;
  border: 3px solid #000;
  padding: 5px;
}

.moreBtn {
  color: #777;
  font-size: 18px;
  display: flex;
  width: 100%;
  margin-top: 35px;
  align-items: center;
  justify-content: center;
  white-space: nowrap;
  &:before,&:after {
    content: '';
    display: block;
    width: 100%;
    height: 1px;
    background-color: #777;
    margin: 0 15px;
  }
}

.pipe {
  &:before {
    content: '';
    display: block;
    width: 1px;
    height: 35px;
    background-color: #000;
  }
}

.todo {
  &Form {
    width: 900px;
    margin: 0 auto;
    padding: 78px 50px 50px;
    border: 3px solid #000;
    border-radius: 15px;
    box-shadow: 5px 5px #ccc
  }
  &_name {
    &-input {
      width: 250px;
      border: 3px solid #000;
      transition: .3s;
      padding: 5px;
      &:focus {
        border-color: hsl(189, 100%, 82%);
        transform: scale(1.01);
      }
    }
  }
    &_form {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
    }
  &_date {
    display: block;
    width: 200px;
    margin-left: 30px;
    @extend %date;
  }
  &_submit {
    @extend %formBtn;
    margin-left: auto;
    margin-top: 25px;
  }
  &_select {
    margin-left: 20px;
    display: flex;
    align-items: center;
    &-body {
      display: inline-block;
      margin-left: 15px;
      border: 3px solid #000;
      position: relative;
      &:after {
        content: '';
        display: inline-block;
        width: 5px;
        height: 5px;
        border-bottom: 1px solid #000;
        border-right: 1px solid #000;
        position: absolute;
        top: 50%;
        right: 5px;
        transform: translateY(-50%) rotate(45deg);
      }
    }
    &-area {
      padding: 4px 20px;
      font-size: 14px;
      vertical-align: top;
    }
  }
  &_detail {
    margin-top: 25px;
    &-item {
      padding: 8px 0;
    }
    &-contents {
      display: flex;
      align-items: center;
      input {
        @extend %date;
        border-width: 1px;
        margin-left: 50px;
      }
    }
    &-add {
      input {
        margin-top: 15px;
        @extend %date;
      }
      button {
        margin-top: 15px;
        margin-left: 20px;
        @extend %formBtn;
      }
    }
    &-date {
      @extend %date;
      margin-left: 15px;
    }
  }
}

// animation 
.Down {
  &-enter {
    opacity: 0;
    &-to {
      opacity: 1;
    }
    &-active {
      transition: opacity .5s;
    }
  }
  &-leave {
    opacity: 1;
    &-to {
      opacity: 0;
    }
    &-active {
      transition: all .3s;
    }
  }
}

@keyframes silideDown {
  from {
    opacity: 0;
  }
  25% {
    opacity: 1;
  }
  to {
    opacity: 1;
  }
}
</style>