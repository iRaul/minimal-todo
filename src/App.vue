<template>
  <div id="app">

    <a href="https://github.com/iRaul/vue-minimal-todo" target="_blank" class="github-corner" aria-label="View source on Github"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#1dd1a1; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a>

    <div class="container mx-auto">
      <div class="mx-auto w-full md:w-1/2 py-8 px-4">
        
        <!-- Title -->
        <div class="flex items-center mb-6">
          <h1 class="tracking-wide text-white mr-6"><span>MINIMAL</span> TO DO</h1>

          <img src="/src/assets/logo.svg" class="hidden md:block" width="40" alt="">
        </div>

        <!-- New Todo Input -->
        <div class="input-wrapper relative">
          <input type="text"
                v-model="newTodo"
                v-on:keyup.enter="addTodo"
                v-on:focus="show_guide = true"
                v-on:blur="show_guide = false"
                placeholder="What needs to be done today?"
                class="p-4 mb-4 w-full bg-transparent border-grey-light text-white border rounded">

          <transition name="fade">
            <span class="text-guide text-grey-darker absolute text-xs" v-show="show_guide">Press Enter</span>       
          </transition>   
        </div>
      
        <!-- To Do List -->    
        <ul class="list-reset">
          <transition-group name="fade">
            <li v-for="todo in todos" :key="todo.id"
                class="py-6 px-2 border-b border-grey-darkest flex justify-between items-center relative todo__item">
              <div>
                <input type="checkbox" :id="todo.id" class="cbx hidden" v-model="todo.completed">
                <label :for="todo.id" class="text-xl cbx__child"></label>
                <label :for="todo.id" class="cbx__lbl text-white inline-block mt-1" :class="{ completed: todo.completed }">{{ todo.title }}</label>
              </div>
              <button v-on:click="removeTodo(todo)" type="button" class="flex items-center delete-button absolute pin-r">
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
      todos: [],
      show_guide: false
    }
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
    },
    removeTodo(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1)
    }
  },

  mounted() {
    if(localStorage.getItem(STORAGE_KEY)) this.todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
  },

  watch: {
    todos: {
      handler() {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(this.todos));
      },
      deep: true
    }
  }
}
</script>

<style lang="scss">

  $c-primary: #1dd1a1;

  // Global
  body { font-family: 'Raleway', sans-serif; }

  // Github Corner
  .github-corner {
    @media (max-width: 768px) {
      display: none;
    }
  }

  .github-corner:hover .octo-arm {
  	animation: octocat-wave 560ms ease-in-out
  }

  @keyframes octocat-wave {
    0%, 100% { transform: rotate(0) }
    20%, 60% { transform: rotate(-25deg) }
    40%, 80% { transform: rotate(10deg) }
  }
  
  // Title
  h1 { 
    span { color: $c-primary; }  
  }

  // Input
  .input-wrapper {
    input {
      transition: border-color .3s ease; 

      &:focus {
        outline: none;
        border-color: $c-primary;      
      }
    }
    
    .text-guide {
      right: 0;
      bottom: -8px;
      text-transform: uppercase;
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
    transition: opacity .3s;
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
      &:last-child {
        border-bottom: none;
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
