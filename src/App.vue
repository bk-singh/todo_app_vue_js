<template>
  <div id="app" class="row">
    <div class="col-md-6 todo-outer">
      <div v-if="errors && errors.length"  class="alert alert-danger">
            <b>Please correct the following errors:</b>
            <ul>
                <li v-for="error in errors">{{ error }}</li>
            </ul>
      </div>
      <div class="page-header text-center">
          <h1 class="app-name"> todos app</h1>
      </div>
      <div class=" add-todo-item">
      <button class="btn btn-info"   @click="showAddTodo= !showAddTodo;">
          <span class="glyphicon glyphicon-plus"  v-show="!showAddTodo" v-cloak></span>
          <span v-show="!showAddTodo" v-cloak> Add a todo</span>

          <span  v-show="showAddTodo" v-cloak> Cancel </span>

      </button>
      <input class="new-todo add-todo-item input-lg"
         type='text'
         v-show="showAddTodo" v-cloak
         autofocus autocomplete="off"
         placeholder="Add a todo item "
         v-model="newTodo"
         @keyup.enter="addTodo" >

      </div>

          <nav class="navbar navbar-inverse">
            <div class="container-fluid">
              <ul class="nav navbar-nav">
                    <li :class="{ active: option == 'All'}">
                        <a href="#" @click="option='All', getTodosList();" > All</a>
                    </li>
                    <li :class="{ active: option == 'Incomplete'}">
                        <a  href="#" @click="option='Incomplete';getTodosList();" > Incomplete</a>
                    </li>
                    <li :class="{ active: option == 'Completed'}">
                        <a href="#" @click="option='Completed'; getTodosList();"> Completed</a>
                    </li>
              </ul>
            </div>
          </nav>
          <div class="">
            <div class= "todo-list-item" v-show="option == 'All'" v-cloak>
                  <input type="checkbox" value="111" @click="changeAllStatusTodo" v-model="selectAll"> Mark all todos completed
          </div>
              <div class="text-center" v-show="!todosItems.length" v-cloak> No item to display </div>
              <div  v-for="todoItem in todosItems" class="list-group">
                <todo-item :class= "{'list-group-item': !todoItem.isCompleted, 'well well-sm': todoItem.isCompleted}"
                  :todoItem="todoItem"
                  v-on:removeTodoEvent="removeTodo(todoItem)"
                  v-on:changeStatusTodoEvent="changeTodoStatus(todoItem)"
                  >
                </todo-item>
              </div>
            </div>
            <hr>
            <div class="todo-footer">
              <div class="container-fluid row">
                  <div class="col-sm-9">
                        <span>{{ todosItems.length }} items</span>
                  </div>
                  <div class="col-sm-3" v-show="todosItems.length && option !== 'Incomplete'" v-cloak>
                      <a href="#" @click="clearCompletedTodo()"> Clear completed</a>
                  </div>
              </div>
            </div>
      </div>
  </div>
</template>

<script>
import TodoItemComponent from './TodoItemComponent.vue'
export default {
  name: 'App',
  components:{
   'todo-item': TodoItemComponent
  },
  data () {
    return {
      newTodo: '',
      todos: [],
      selectAll: false,
      option:'All',
      errors:[],
     todosItems:[],
     showAddTodo:false,
    }
  },
created(){
    this.option= 'All';
    this.todosItems = this.getTodosList();
    this.count = localStorage.getItem("count") || this.todos.length;

},
    computed: {
        countTodoItems: function () {
          return  this.todos.filter( aTododItem => aTododItem.isCompleted == false).length;
        },
      },
  watcher:{
      todos:  function (newTodos, oldTodos) {
            this.todosItems = this.getTodosList();
        return
      }
    },
  methods: {
  validateNewTodo(){
        this.errors = [];
        if(!this.newTodo)
            this.errors.push("New todo required.");
        if(this.newTodo && this.newTodo.length < 3)
            this.errors.push("Minimum new todo length should be 3.");
        if(this.newTodo && this.newTodo.length > 50)
            this.errors.push("New todo length can not be more than 50 chars.");
        return this.errors.length == 0
  },
  addTodo(){
        if(!this.validateNewTodo()) return false;
        this.todos.unshift({id: this.count, text: this.newTodo, isCompleted: false});
        this.newTodo = '';
        this.count++;
        this.showAddTodo = false;
        this.setTodosList();
  },
  removeTodo(todoItem){
        var index = this.todos.map(function(e) { return e.id; }).indexOf(todoItem.id);
        if(index> -1)  this.todos.splice(index,1);
        this.setTodosList();

    },
  changeAllStatusTodo(){
          if(!this.selectAll){
              this.todos.forEach( tododItem => tododItem.isCompleted = true);
          }
          else{
              this.todos.forEach( tododItem => tododItem.isCompleted = false);
          }
          this.setTodosList();

    },
    clearCompletedTodo(){
            this.todos =   this.todos.filter( aTododItem => aTododItem.isCompleted == false);
            this.setTodosList();
      },
    checkSelectAll(todoItem) {
          this.selectAll = false;
          if(this.todos && this.todos.every(aTododItem => aTododItem.isCompleted == true)){
              this.selectAll = true;
          }
      },
      changeTodoStatus(todoItem){
            var index = this.todos.findIndex(aTodo => aTodo.id == todoItem.id);
            if(index> -1)
                  this.todos[index].isCompleted = !this.todos[index].isCompleted;
            this.setTodosList();
      },
      getTodosList() {
            var todosItems = [];
            this.todos = JSON.parse(localStorage.getItem("todos") || "[]");
            if(this.option == 'Incomplete'){
                    todosItems = this.todos.filter(aTodoItem => aTodoItem.isCompleted == false)
            }else if(this.option=='Completed'){
                  todosItems = this.todos.filter(aTodoItem => aTodoItem.isCompleted == true)
            }else{
              todosItems = this.todos;
            }
            this.todosItems = todosItems;
            return todosItems;
      },
      setTodosList() {
            localStorage.setItem("todos", JSON.stringify(this.todos) || "[]");
            localStorage.setItem("count", this.count || this.todos.length);
            this.getTodosList() ;
      },
  }
}
</script>

<style>
#app {
  color: #2c3e50;
}

.app-name{
  color: pink;
}

.new-todo{
    width: 70%;
    height: 50px;
    border: 2px solid gray;
    font-size: 2em;
}
.add-todo-item {
    padding: 10px 10px 10px 10px;
    margin-bottom: 15px;
}

.todo-outer{
  border: 2px solid grey;
  border-radius: 2%;
  margin :  20px 20px 20px 20px ;
  padding : 20px 20px 20px 20px ;
  white-space: nowrap;
  overflow: hidden;
}
.todo-list {
    text-align: left;
}
.todo-list-item{
    margin :  10px 10px 10px 10px ;
  }
.todo-completed{
    text-decoration: line-through;
}
</style>
