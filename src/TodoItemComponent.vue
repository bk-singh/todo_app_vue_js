<template>
  <div id="TodoItemComponent" class="row">
      <div class="col-md-1"> <input type="checkbox" value="todoItem.isCompleted" v-model="todoItem.isCompleted" @click="changeStatusTodo()"></div>
      <div  class="col-md-9" v-bind:class="{ 'todo-completed': todoItem.isCompleted }"> {{todoItem.text}} </div>
      <div class="col-md-2" @click="removeTodo()"> <span class="glyphicon glyphicon-trash text-danger"> </span></div>
  </div>
</template>

<script>
  export default {
    name: 'TodoItemComponent',
    props:{
        todoItem:{
          type:Object
        }
      },
    data () {
      return {
      }
    },
    methods: {
      removeTodo(){
              this.$emit('removeTodoEvent');
        },
        changeStatusTodo(){
              this.$emit('changeStatusTodoEvent');
        }
      }
  }
</script>
