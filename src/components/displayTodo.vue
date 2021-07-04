<template>
  <div class="displayTodo">
    <div class="btns">
      <button
        @click="updateType('99', '99')"
        :class="{'active': currentTab === '99'}"
      >
        全てのTODO
      </button>
      <button
        @click="updateType('1', '1')"
        :class="{'active': currentTab === '1'}"
      >
        緊急度: 高<br>
        重要度: 高
      </button>
      <button
        @click="updateType('1', '0')"
        :class="{'active': currentTab === '2'}"
      >
        緊急度: 高<br>
        重要度: 低
      </button>
      <button
        @click="updateType('0', '1')"
        :class="{'active': currentTab === '3'}"
      >
        緊急度: 低<br>
        重要度: 高
      </button>
      <button
        @click="updateType('0', '0')"
        :class="{'active': currentTab === '4'}"
      >
        緊急度: 低<br>
        重要度: 低
      </button>
    </div>
    <div class="todoArea">
      <ul class="todoArea_list">
        <li
          v-for="item in resultTodo"
          :key="item.id"
        >
        <input
          type="text"
          v-model="item.todoName"
          class="todoArea_name"
        >
        <div class="todoArea_wrapper">
          <p class="levelText"><span>重要度:</span>{{levelText(item.important)}}</p>
          <p class="levelText"><span>緊急度:</span>{{levelText(item.emergency)}}</p>
          <input 
            type="datetime-local" 
            v-model="item.todoDate"
            class="todoArea_input"
          >
          <button 
            class="removeBtn"
            @click="removeTodo(item.Uid)"
          >
          REMOVE
          </button>
        </div>
        <button 
          class="detailBtn" 
          @click="toggleDetailOpen(item.Uid)"
        >
        {{detailOpentxt}}
        </button>
        <template
          v-if="isDetailOpen.indexOf(item.Uid) >= 0"
        >
          <ul class="todoArea_low">
            <li
              v-for="(detailItem, detailIndex) in item.detailArr"
              :key="detailItem.id"
              class="pipe"
            >
              <input 
                type="text" 
                v-model="detailItem.name"
                class="todoArea_input_name"
              >
              <input 
                type="datetime-local" 
                v-model="detailItem.date"
                class="todoArea_input"
              >
              <button
                class="removeBtn"
                @click="removeDetail(item.Uid, detailIndex)"
              >
              REMOVE
              </button>
            </li>
            <div class="todoArea_low-add pipe">
              <input :ref="'detailName' + item.Uid" type="text">
              <input :ref="'detailDate' + item.Uid" type="datetime-local" class="todoArea_low-date">
              <button @click="pushDetail(item.Uid)">追加</button>
            </div>
          </ul>
        </template>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'displayTodo',
  props: {
    todos: {
      type: Array
    }
  },
  data() {
    return {
      todo: this.todos,
      emergencyFlg: '1',
      importantFlg: '1',
      currentTab: '1',
      isDetailOpen: [],
      existDetailOpen: false,
    }
  },
  methods: {
    updateType: function(e, i) {
      this.emergencyFlg = e
      this.importantFlg = i

      if(e === '99' && i === '99') this.currentTab = '99'
      if(e === '1' && i === '1') this.currentTab = '1'
      if(e === '1' && i === '0') this.currentTab = '2'
      if(e === '0' && i === '1') this.currentTab = '3'
      if(e === '0' && i === '0') this.currentTab = '4'
    },
    getTodoTarget(Uid) {
      const result = this.todo.filter(item => item.Uid === Uid)[0]
      return result
    },
    removeTodo: function(todoUid) {
      const result = this.getTodoTarget(todoUid)
      const resultIndex = result.id
      this.$emit('removeTodo', resultIndex)
    },
    pushDetail: function(todoUid) {
      const result = this.getTodoTarget(todoUid)
      const resultIndex = result.id

      var targetName = this.$refs['detailName' + todoUid][0]
      var targetDate = this.$refs['detailDate' + todoUid][0]

      this.$emit('pushDetail', targetName.value, targetDate.value, resultIndex)

      targetName.value = ""
      targetDate.value = ""
    },
    removeDetail: function(todoUid, detailIndex) {
      const result = this.getTodoTarget(todoUid)
      const resultIndex = result.id

      this.$emit('removeDetailTodo', resultIndex, detailIndex)
    },
    toggleDetailOpen: function(target) {
      if(this.isDetailOpen.indexOf(target) >= 0) {
        this.isDetailOpen = this.isDetailOpen.filter(item => item !== target)
        this.existDetailOpen = false
      } else {
        this.isDetailOpen.push(target)
        this.existDetailOpen = true
      }
    },
  },
  computed: {
    resultTodo: function() {
      let resultArr
      if(this.emergencyFlg === '99' && this.importantFlg === '99' ) {
        resultArr = this.todo
      } else {
        resultArr = this.todo.filter(item => item.emergency == this.emergencyFlg && item.important == this.importantFlg)
      }
      return resultArr
    },
    levelText: function() {
      return function(level) {
        let result
        if(level == '0') result = '低'
        if(level == '1') result = '高'
        return result
      }
    },
    detailOpentxt: function() {
      let result
      if(this.existDetailOpen) result = '-'
      if(!this.existDetailOpen) result =  '+'
      return result
    }
  },
  updated: function() {
    console.log(this.todo)
  }
}
</script>

<style lang="scss" scoped>
%date {
  width: 260px;
  font-size: 14px;
  border: 3px solid #000;
  padding: 5px;
}
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
.pipe {
  &:before {
    content: '';
    display: block;
    width: 1px;
    height: 35px;
    background-color: #000;
  }
}
  .displayTodo {
    width: 100%;
    margin-left: 50px;
    overflow: hidden;
  }
  .btns {
    display: flex;
    margin-top: 50px;
    button {
      flex-grow: 1;
      padding: 15px 0;
      text-align: center;
      border: 3px solid #000;
      border-radius: 15px;
      box-shadow: 5px 5px #ccc;
      margin-left: 5px;
      transition: all .5s;
      font-size: 14px;
      &.active {
        background-color: #222;
        color: #fff;
      }
    }
  }
  .todoArea {
    width: 100%;
    border: 3px solid #000;
    border-top: 0px;
    padding: 35px 20px;
    font-size: 14px;
    &_list {
      li {
        transition: .5s;
        padding: 25px 0;
        border-top: 1px solid #666;
        &:first-child {
          border-top: 0;
        }
      }
    }
    &_name {
      width: 80%;
      font-size: 18px;
      font-weight: bold;
    }
    &_wrapper {
      display: flex;
      align-items: center;
      margin-top: 15px;
    }
    .levelText {
      display: inline-block;
      font-size: 18px;
      font-weight: bold;
      margin-left: 70px;
      &:first-child {
        margin-left: 0;
      }
      &.high {
        color: red;
      }
      &.low {
        color: blue;
      }
      span {
        font-size: 14px;
        font-weight: normal;
      }
    }
    .todoArea_input {
      width: 200px;
      border: 2px solid #666;
      padding: 5px;
      margin-left: 80px;
    }
    .removeBtn {
      padding: 5px 15px;
      border: 1px solid #666;
      text-align: center;
      border-radius: 50px;
      margin-left: 50px;
      transition: .5s;
      &:hover {
        background-color: #000;
        color: #fff;
      }
    }
    .detailBtn {
      width: 40px;
      text-align: center;
      border: 1px solid #222;
      font-size: 30px;
      padding-bottom: 6px;
      margin-top: 20px;
    }
    .todoArea_low {
      li {
        padding: 25px 0;
        border-top: 0;
      }
      .todoArea_input_name {
        border-bottom: 2px solid #222;
        padding: 7px;
      }
      &-add {
        margin-top: 20px;
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
</style>
