<template>
  <section class="todo-sec">
    <header class="todo-header">
      <!-- 标题 -->
      <h1 class="todo-title">todos</h1>
      <div class="input-box">
        <input @keyup.enter="addList" class="todo-input" v-model="todoValue" type="text" placeholder="What needs to be done?">
        <label @click="checkAll" class="clicl-all" v-if="todoList.length"></label>
      </div>
    </header>

    <section class="main-sec">
      <template v-for="(item, index) in todoList" :key="`todoList${index}`">
        <todo-list-item v-if="item.checked === cond || cond === 0" :todoTitle="item.todoTitle"
        :index="index" :checked="item.checked"
        @changeCheck="changeCheck" @delItem="delItem"></todo-list-item>
      </template>
    </section>

    <footer class="todo-footer" v-if="todoList.length">
      <div class="tools-box">
        <span>
          {{left}} items left
        </span>

        <div class="filters">
          <span class="filter-button" @click="changeFilter(0)"
          :class="{'click': filter === 0}"> 
            All
          </span>
          <span class="filter-button" @click="changeFilter(1)"
          :class="{'click': filter === 1}">
            Active
          </span>
          <span class="filter-button" @click="changeFilter(2)"
          :class="{'click': filter === 2}">
            Completed
          </span>
        </div>

        <span class="clear-all" @click="delCheckedAll"
        :class="{'show': left !== todoList.length}"> 
          Clear completed
        </span>
      </div>
    </footer>
  </section>
</template>

<script>
import { reactive, toRefs } from '@vue/reactivity'
import todoListItem from '../components/todoListItem'
import { computed } from '@vue/runtime-core'

export default {
  setup () {
    const state = reactive({
      // todoList
      todoList: [],
      // input 值
      todoValue: '',
      // 筛选条件
      cond: computed(() => {
        if (!state.filter) {
          return 0
        } else if (state.filter === 1) {
          return false
        } else {
          return true
        }
      }),
      // 目前筛选器
      filter: 0,
      /**
       * 更改筛选器,点击触发
       * @event
       * 
       * @param {number} num
       */
      changeFilter: num => {
        state.filter = num
      },
      // 未完成任务数量
      left: computed(() => {
        let cont = 0
        state.todoList.forEach(ele => {
          if (ele.checked) {
            cont++
          }
        })

        return state.todoList.length - cont
      }),
      /**
       * 添加到todo中，按下回车触发
       * @event
       * 
       */
      addList: () => {
        let todoValue = state.todoValue 
        todoValue ? state.todoList.push(
          {
            todoTitle: todoValue,
            checked: false
          }) : ''
        
        state.todoValue = ''
      },
      /**
       * 全选或全不选，点击时触发
       * 
       * @event
       */
      checkAll: () => {
        if (state.left !== state.todoList.length) {
          state.todoList.forEach(ele => {
            ele.checked = false
          })
        } else {
          state.todoList.forEach(ele => {
            ele.checked = true
          })
        }
      },
      /**
       * 更改勾选状态
       * 
       * @param {number} index
       */
      changeCheck: index => {
        state.todoList[index].checked = !state.todoList[index].checked
      },
      /**
       * 删除item
       * 
       * @param {number} index
       */
      delItem: index => {
        state.todoList.splice(index, 1)
      },
      /**
       * 删除已选item，点击时触发
       * @event
       */
      delCheckedAll: () => {
        state.todoList = state.todoList.filter(ele => {
          if (!ele.checked) {
            return ele
          }
        })
      }
    })
    
    return {
      ...toRefs(state)
    }
  },
  components: {
    todoListItem
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/css/common.scss';
$sec-w: 550px;
$footer-font-c: #777;
$check-all-w: 30px;

.todo-sec {
  font-family: $font-f;
  color: $font-c;
  min-height: calc(100vh - 40px);
  width: calc(100vw - 40px);
  background-color: $back-c;
  padding: 20px;

  // 头部区域
  .todo-header {
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: $sec-w;

    .todo-title {
      font-size: 100px;
      font-weight: 100;
      color: $todos-c;
      margin: 0 0 40px;
    }
  
    .input-box {
      position: relative;
      width: 100%;

      .todo-input {
        position: relative;
        font-size: $font-si;
        font-weight: 100;
        color: $font-c;
        padding: 16px 16px 16px 60px;
        border: none;
        box-shadow: $shadow;
        width: calc(100% - 76px);
        outline: none;
  
        &::placeholder {
          font-weight: 100;
          color: $pla-c;
          font-style: $pla-s;
        }
      }

      .clicl-all {
        position: absolute;
        top: 24%;
        left: 10px;
        height: $check-all-w;
        width: $check-all-w;
        z-index: 3;
        cursor: pointer;

        &::after {
          content: '❯';
          position: absolute;
          top: calc(50% + 9px);
          left: -20px;
          display: block;
          width: 60px;
          height: 34px;
          font-size: 22px;
          color: #e6e6e6;
          transform: rotate(90deg);
        }
      }
    }
  }

  // list区域
  .main-sec {
    display: flex;
    flex-direction: column;
    width: $sec-w;
    margin: 0 auto;
  }

  // 页脚显示
  .todo-footer {
    position: relative;
    margin: 0 auto;
    box-shadow: $shadow;
    background-color: #fff;
    width: $sec-w - 30px;
    color: $footer-font-c;
    font-weight: 100;
    padding: 10px 15px;
    height: 20px;
    user-select: none;

    &::before {
      content: '';
      position: absolute;
      right: 0;
      bottom: 0;
      left: 0;
      height: 50px;
      overflow: hidden;
      box-shadow: 0 1px 1px rgb(0 0 0 / 20%), 
      0 8px 0 -3px #f6f6f6, 
      0 9px 1px -3px rgb(0 0 0 / 20%), 
      0 16px 0 -6px #f6f6f6, 
      0 17px 2px -6px rgb(0 0 0 / 20%);
    }

    .tools-box {
      position: absolute;
      display: flex;
      justify-content: space-around;
      margin: 0 auto;
      width: 100%;
    }

    .filters {

      .filter-button {
        margin: 3px;
        padding: 3px 7px;
        text-decoration: none;
        border: 1px solid transparent;
        border-radius: 3px;
        cursor: pointer;
        transition: color .4s;
      }

      .click {
        border-color: rgba(175, 47, 47, 0.2);
      }
    }

    .clear-all {
      cursor: pointer;
      opacity: 0;
    }

    .show {
      opacity: 1;
    }
  }
}
</style>