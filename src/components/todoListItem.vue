<template>
  <div class="todo-item-sec">
    <input @click="changeCheck" type="checkbox" class="todo-item-check"
    :class="{'todo-item-checked': checked}">
    <label class="todo-title"
    :class="{'clear': checked}">
      {{todoTitle}}
    </label>

    <div @click="delTodo" class="del-button"></div>
  </div>
</template>

<script>
import { reactive, toRefs } from '@vue/reactivity'
export default {
  // 添加todo，当前index，当前完成状态
  props: ['todoTitle', 'index', 'checked'],
  emits: ['changeCheck', 'delItem'],
  setup (props, cxt) {
    const state = reactive({
      /**
       * 切换勾选状态，点击时触发
       * @event
       */
      changeCheck: () => {
        cxt.emit("changeCheck", props.index)
      },
      /**
       * 删除item，点击时触发
       * @event
       */
      delTodo: () => {
        cxt.emit("delItem", props.index)
      }
    })

    return {
      ...toRefs(state)
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/css/common.scss';
$chek-h: 40px;
$del-button-c: #af5b5e;

.todo-item-sec {
  position: relative;
  font-size: $font-si;
  font-family: $font-f;
  font-weight: 100;
  background-color: #fff;
  width: 100%;
  box-shadow: $shadow;
  
  .todo-item-check {
    text-align: center;
    width: $chek-h;
    height: $chek-h;
    position: absolute;
    top: 0;
    bottom: 0;
    margin: auto 0;
    border: none;
    -webkit-appearance: none;
    appearance: none;
    background-image: url('../assets/img/check.svg');
    background-repeat: no-repeat;
    background-position: center left;
    cursor: pointer;
    z-index: 9;
  }

  .todo-item-checked {
    background-image: url('../assets/img/checked.svg');
  }

  .todo-title {
    overflow: hidden;
    display: block;
    word-break: break-all;
    padding: 15px 15px 15px 60px;
    line-height: 1.2;
    transition: color 0.4s;
    // height: 1rem;
    // line-height: 1rem;
    // width: 100%;
    // white-space: nowrap;
    // text-overflow: ellipsis;
  }

  .clear {
    color: #d9d9d9;
    text-decoration: line-through;
  }

  .del-button {
    opacity: 0;
    position: absolute;
    top: 0;
    right: 10px;
    bottom: 0;
    width: 40px;
    height: 40px;
    margin: auto 0;
    font-size: 30px;
    color: $del-button-c;
    margin-bottom: 11px;
    transition: all 0.2s ease-out;
    cursor: pointer;
    
    &::after {
      content: '×';
    }

    &:hover {
      opacity: 1;
      z-index: 10;
    }
  }
}
</style>