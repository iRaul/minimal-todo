<template>
  <div id="app">

    <div class="container mx-auto">
      <div class="mx-auto w-full md:w-1/2 py-8 px-4">
        
        <!-- Title -->
        <h1 class="mb-6 tracking-wide text-center text-white"><span>MINIMAL</span> TO DO</h1>

        <!-- New Todo Input -->
        <input type="text"
              v-model="newTodo"
              v-on:keyup.enter="addTodo"
              placeholder="What needs to be done today?"
              class="p-4 mb-4 w-full bg-transparent border-grey-light text-white border rounded">
      
        <!-- To Do List -->    
        <ul class="list-reset">
          <transition-group name="fade">
            <li v-for="todo in todos" :key="todo.id"
                class="py-4 px-2 mb-4 border-b border-grey-dark flex justify-between items-center todo__item">
              <div>
                <input type="checkbox" :id="todo.id" class="cbx hidden" v-model="todo.completed">
                <label :for="todo.id" class="text-xl cbx__child"></label>
                <label :for="todo.id" class="cbx__lbl text-white" :class="{ completed: todo.completed }">{{ todo.title }}</label>
              </div>
              <button v-on:click="removeTodo(todo)" type="button" class="flex items-center delete-button">
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-x"><line x1="18" y1="6" x2="6" y2="18"></line><line x1="6" y1="6" x2="18" y2="18"></line></svg>
              </button>
            </li>          
          </transition-group>
        </ul>

      </div>
    </div>

  </div>
</template>

<script>
const STORAGE_KEY = 'todo-storage';


export default {
  name: 'app',
  data() {
    return {
      newTodo: '',
      todos: []
    }
  },
  
  created() {
    this.todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
  },

  methods: {
    addTodo() {
      if(this.newTodo.length) {
        this.todos.push({
          title: this.newTodo,
          completed: false,
          id: this.todos.length
        })
      }
  
      this.newTodo = '';
      
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos))
    },
    removeTodo(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1)
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos))
    }
  }
}
</script>

<style lang="scss">

  $c-primary: #1dd1a1;

  // Global
  body { font-family: 'Raleway', sans-serif; }

  // Title
  h1 { 
    span { color: $c-primary; }  
  }

  // Input
  input {
    transition: border-color .3s ease; 
    &:focus {
      outline: none;
      border-color: $c-primary;
    }
  }

  // Checkbox & Label
  .cbx__lbl {
    margin-left: 4px;
    vertical-align: middle;
    cursor: pointer;
    position: relative;

    &:after {
      content: '';
      width: 0%; height: 2px;
      background: $c-primary;
      position: absolute;
      left: 0; top: 50%;
      display: block;
      transition: all .4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }

    &.completed {
      color: #c8ccd4;
      &:after {
        width: 100%;
      }
    }
  }

  .cbx:checked ~ .cbx__child {
    border-color: transparent;
    background: $c-primary;
    animation: jelly 0.6s ease;
  }
  
  .cbx:checked ~ .cbx__child:after {
    opacity: 1;
    transform: rotate(45deg) scale(1);
  }
 
  @keyframes jelly {
    from { transform: scale(1, 1); }
    30% { transform: scale(1.25, 0.75); }
    40% { transform: scale(0.75, 1.25); }
    50% { transform: scale(1.15, 0.85); }
    65% { transform: scale(0.95, 1.05); }
    75% { transform: scale(1.05, 0.95); }
    to { transform: scale(1, 1); }
  }

  .cbx__child {
    position: relative;
    top: 1px;
    width: 17px;
    height: 17px;
    border: 1px solid #c8ccd4;
    border-radius: 3px;
    vertical-align: middle;
    display: inline-block;
    transition: background 0.1s ease;
    cursor: pointer;

    &:after {
      content: '';
      position: absolute;
      top: 1px;
      left: 5px;
      width: 5px;
      height: 11px;
      opacity: 0;
      transform: rotate(45deg) scale(0);
      border-right: 2px solid #fff;
      border-bottom: 2px solid #fff;
      transition: all 0.3s ease;
      transition-delay: 0.15s;
    }
  }

  // Fade In/Out Animation
  .fade-enter-active, 
  .fade-leave-active {
    transition: opacity .5s;
  }
  
  .fade-enter, 
  .fade-leave-to {
    opacity: 0;
  }

  // Todo List
  .todo {
    &__item {
      &:hover {
        .delete-button {
          opacity: 1;
          visibility: visible;
        }
      }
    }
  }

  // Delete Button
  .delete-button {
    opacity: 0;
    visibility: hidden;
    transition: opacity .3s ease;

    svg { 
      transition: all .3s ease; 
      height: 20px;
    }
    
    .feather { color: #fff; }
    &:hover .feather { color: #ff6b6b; }
    
    &:focus {
      outline: none;
      border: 1px dashed #ff6b6b;
    }
  }
</style>
