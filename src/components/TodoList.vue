<template>
    <div>
        <input type="text" class="todo-input" placeholder="whats need to be done"
        v-model="newTodo" @keyup.enter="addTodo">
        <!-- rendering view to the browser  looping through todos-->
        <transition-group name="fade" enter-active-class="animated fadeInUp" 
        leave-active-class="animated fadeOutDown">
            <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
                <input type="checkbox" v-model="todo.completed">
                <div class="todo-item-left">
                    <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label"
                    :class="{ completed : todo.completed}">
                        {{ todo.title }}
                    </div>
                    <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)"
                    @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
                </div>
                <div class="remove-item" @click="removeTodo(index)">
                    &times;
                </div>
            </div>
        </transition-group>

        <div class="extra-container">
            <div><label><input type="checkbox" :checked="!anyRemaining"
            @change="checkAllTodos" >Check All</label></div>
            <div>{{ remaining }} items left</div>
        </div>

        <div class="extra-container">
            <button :class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
            <button :class="{ active: filter == 'active'}" @click="filter = 'active'">Active</button>
            <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
        </div>

        <transition name="fade">
            <div>
                <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
            </div>
        </transition>
        
    </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
        // handling input data
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
          {
              'id': 1,
              'title': 'Listen to some cool music by zain',
              'completed': false,
              'editing': false
          },
          {
              'id': 2,
              'title': 'Make some pull request on git',
              'completed': false,
              'editing': false
          }
      ]
    }
  },
    //   computed section
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length;
        },

        anyRemaining() {
            return this.remaining != 0;
        },
        // handling the filter events
        todosFiltered() {
            if(this.filter == 'all') {
                return this.todos;
            }
            else if (this.filter == 'active') {
                return this.todos.filter(todo => !todo.completed);
            }
            else if (this.filter == 'completed') {
                return this.todos.filter(todo => todo.completed); 
            }

            return this.todos;
        },

        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0;
        },
    },

    //   handling the focus events
    directives: {
        focus: {
            inserted: function (el) {
                el.focus();
            }
        }
    },

    // handling events listener
    methods: {
        addTodo() {
            // anytime referencing a data property use this.
            // handling empty todo strings
            if (this.newTodo.trim().length == 0) {
                return;
            }

            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
            })

            // setting the input to nothing
            this.newTodo = '',
            this.idForTodo++
        },

        // handling the edit method
        editTodo(todo) {
            this.beforeEditCache = todo.title;
            todo.editing = true;
        },

        // handling the remove method
        removeTodo(index) {
            this.todos.splice(index, 1);
        },

        // handling the blur event when edit is done
        doneEdit(todo) {
            if (todo.title.trim() == '') {
                todo.title = this.beforeEditCache;
            }

            todo.editing = false;
        },

        //handling the escape events
        cancelEdit(todo) {
            todo.title = this.beforeEditCache;
            todo.editing = false;
        },

        // handling the check events
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked);
        },

        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed); 
        }
    }
}
</script>

<style lang="scss">

    @import url('https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css');

    .todo-input {
        width: 100%;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;

        &:focus {
            outline: 0;
        }
    }

    .todo-item {
        margin-bottom: 12px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        animation-duration: 0.3s;
    }

    .remove-item {
        cursor: pointer;
        margin-left: 14px;
        &:hover {
            color: black;
        } 
    }

    .todo-item-edit {
        font-size: 24px;
        color: #22cc33;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;

        &:focus {
            outline: none;
        } 
    }

    .completed {
        text-decoration: line-through;
        color:rgb(0, 68, 255);
    }

    .extra-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgrey;
        padding-top: 14px;
        margin-bottom: 14px;
    }

    button {
        font-size: 14px;
        background-color: white;
        appearance: none;

        &:hover {
            background: lightgreen;
        }

        &:focus {
            outline: none;
        }
    }

    .active {
        background: lightgreen;
    }

    // transition effects
    .fade-enter-active, .fade-leave-active {
        transition: opacity .2s;
    }

    .fade-enter, .face-leave-to {
        opacity: 0;
    }
</style>
