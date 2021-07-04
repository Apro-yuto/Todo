<template>
  <div class="todoForm">
    <div v-show="existOpenForm" class="todoFlex">
      <form class="todo_form">
        <div class="todo_name">
          <input
            v-model="todos.todoName"
            type="text"
            name="todoName"
            placeholder="TODO"
            class="todo_name-input"
          >
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
              v-model="todos.emergency"
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
            <input 
              type="text" 
              v-model="item.name"
              class="detailName"
            >
            <input 
              v-model="item.date"
              type="datetime-local"
              class="detailDate"
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
  <div 
    class="slideToggle" 
    @click="existOpenForm = existOpenForm === false ? true : false"
  >
    <img src="@/assets/img/right_arrow.svg" alt=""></div>
  </div>
</template>

<script>
export default {
  name: 'todo-Form',
  data() {
    return {
      existOpenForm: false,
      detailOpen: false,
      detailDate: '',
      detailName: '',
      todos: {
        Uid: '',
        todoName: '',
        important: '',
        emergency: '',
        todoDate: '',
        detailArr: new Array()
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

      this.invalidTodo()
      if(this.error.existError === true) return false

      this.getUniqueStr(2)

      this.$emit('updateTodo',Object.assign({}, this.todos)) // todosの情報をlocalstorageにpush

      this.$set(this.todos, 'todoName' , "")
      this.$set(this.todos, 'important' , "")
      this.$set(this.todos, 'emergency' , "")
      this.$set(this.todos, 'todoDate' , "")
      this.$set(this.todos, 'detailArr' , [])
      this.$set(this.todos, 'detailArr' , [])
    },
    getUniqueStr: function (myStrong){
      var strong = 1000;
      if (myStrong) strong = myStrong;
      var result = new Date().getSeconds().toString(16)  + Math.floor(strong*Math.random()).toString(16)
      this.todos.Uid = result
      console.log({result})
    },
    updateDetail: function(e) {
      e.preventDefault()

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
      this.error.errorArr = new Array()

      let todoName = this.todos.todoName
      let important = this.todos.important
      let emergency = this.todos.emergency
      let todoDate = this.todos.todoDate

      if(todoName.length == 0) {
        this.error.existError = true
        this.error.errorArr.push('項目名を入力してください。')
      }
      if(important === 'default' || important === '') {
        this.error.existError = true
        this.error.errorArr.push('重要度を選択してください。')
      }
      if(emergency === 'default' || emergency === '') {
        this.error.existError = true
        this.error.errorArr.push('緊急度を選択してください。')
      }
      if(todoDate === '') {
        this.error.existError = true
        this.error.errorArr.push('期日を選択してください。')
      }
      else if(this.error.errorArr.length === 0) this.error.existError = false
    },
    invalidDetail: function() {
      this.error.detailErrorArr = new Array()
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
      else if(this.error.detailErrorArr.length === 0) this.error.existDetailError = false
    },
  },
  updated: function() {
    console.log(this.todos, this.detailObj)
  },
}
</script>

<style lang="scss" scoped>

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

.slideToggle {
  width: 35px;
  height: 100%;
  display: flex;
  border: 2px solid #111;
  border-left: 0;
  border-radius: 0 15px 15px 0;
  box-shadow: 3px 3px #ccc;
}

.todo {
  &Form {
    display: flex;
  }
  &Flex {
    width: 500px;
    padding-right: 0;
    margin-left: 5px; 
    border: 3px solid #000;
    padding: 30px;
    border-right: 2px solid #000;
  }
  &_name {
    width: 100%;
    &-input {
      width: 100%;
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
      align-items: center;
    }
  &_date {
    display: block;
    width: 200px;
    @extend %date;
    margin-left: 0; 
    margin-top: 20px;
  }
  &_submit {
    @extend %formBtn;
    margin-left: auto;
    margin-top: 25px;
  }
  &_select {
    display: flex;
    align-items: center;
    margin-top: 20px;
    &:last-of-type {
      margin-left: 20px;
    }
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
      .detailDate {
        @extend %date;
        border-width: 1px;
        margin-left: 50px;
      }
    }
    &-add {
      input {
        margin-top: 25px;
        @extend %date;
        &:first-child {
          width: 100%;
        }
        &:last-child {
          margin-left: 0;
        }
      }
      button {
        margin-top: 15px;
        margin-left: 40px;
        @extend %formBtn;
      }
    }
    &-date {
      @extend %date;
    }
  }
}

</style>
