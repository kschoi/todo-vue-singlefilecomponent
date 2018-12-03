<template>
  <div id="app" class="todoapp">
    <!-- todo header -->
    <todo-header @add:todo="addTodo"></todo-header>
    <!-- //todo header -->
    <!-- todo list -->
		<todo-list
			:todos="todos"
			:filteredTodos="filteredTodos"
			:remaining="remaining"
			@edit:todo="editTodo"
			@remove:todo="removeTodo"
			@done:edit="doneEdit"
			@cancel:edit="cancelEdit">
		</todo-list>
    <!-- todo list -->
    <!-- todo footer -->
		<todo-footer
			:todos="todos"
			:visibility="visibility"
			:remaining="remaining"
			@remove:completed="removeCompleted">
		</todo-footer>
    <!-- //todo footer -->
  </div>
</template>

<script>
import TodoHeader from './components/TodoHeader.vue';
import TodoList from './components/TodoList.vue';
import TodoFooter from './components/TodoFooter.vue';

// localStorage persistence
const STORAGE_KEY = 'todos-vuejs-1.0'
const todoStorage = {
  fetch () {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    todos.forEach(function (todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}
// visibility filters
const filters = {
  all (todos) {
    return todos
  },
  active (todos) {
    return todos.filter(function (todo) {
      return !todo.completed
    })
  },
  completed (todos) {
    return todos.filter(function (todo) {
      return todo.completed
    })
  }
}

export default {
	name: 'app',
	
  data () {
    return {
      todos: todoStorage.fetch(),
      visibility: 'all'
    }
	},

	components: {
		'todo-header': TodoHeader,
    'todo-list': TodoList,
    'todo-footer': TodoFooter,
	},

  computed: {
    filteredTodos () {
      return filters[this.visibility](this.todos)
    },
    remaining () {
      return filters.active(this.todos).length
    },
	},
	
  methods: {
    addTodo (value) {
      this.todos.push({
        id: todoStorage.uid++,
        title: value,
        completed: false
      })
    },

    onHashChange (filter) {
      if (filters[filter]) {
        this.visibility = filter;
      }
    },

    removeTodo (todo) {
      this.todos.splice(this.todos.indexOf(todo), 1)
    },

    editTodo (todo) {
      this.beforeEditCache = todo.title
    },

    doneEdit (todo) {
      todo.title = todo.title.trim()
      if (!todo.title) {
        this.removeTodo(todo)
      }
    },

    cancelEdit (todo) {
      todo.title = this.beforeEditCache
    },

    removeCompleted () {
      this.todos = filters.active(this.todos)
		},
		
		changeFilter () {
			let visibility = window.location.hash.replace(/#\/?/, '')
			if (filters[visibility]) {
				this.visibility = visibility
			} else {
				window.location.hash = ''
				this.visibility = 'all'
			}

		}
	},
	
  watch: {
    todos: {
      handler (todos) {
        todoStorage.save(todos)
      },
      deep: true
    }
	},

	created () {
		window.addEventListener('hashchange', this.changeFilter);
	},

	beforeDestroy () {
		window.removeEventListener('hashchange', this.changeFilter);
	},
}

</script>

<style>
html,
body {
	margin: 0;
	padding: 0;
}

button {
	margin: 0;
	padding: 0;
	border: 0;
	background: none;
	font-size: 100%;
	vertical-align: baseline;
	font-family: inherit;
	font-weight: inherit;
	color: inherit;
	-webkit-appearance: none;
	appearance: none;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}

body {
	font: 14px 'Helvetica Neue', Helvetica, Arial, sans-serif;
	line-height: 1.4em;
	background: #f5f5f5;
	color: #4d4d4d;
	min-width: 230px;
	max-width: 550px;
	margin: 0 auto;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	font-weight: 300;
}

:focus {
	outline: 0;
}

.hidden {
	display: none;
}

.todoapp {
	background: #fff;
	margin: 130px 0 40px 0;
	position: relative;
	box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2),
	            0 25px 50px 0 rgba(0, 0, 0, 0.1);
}

.todoapp input::-webkit-input-placeholder {
	font-style: italic;
	font-weight: 300;
	color: #e6e6e6;
}

.todoapp input::-moz-placeholder {
	font-style: italic;
	font-weight: 300;
	color: #e6e6e6;
}

.todoapp input::input-placeholder {
	font-style: italic;
	font-weight: 300;
	color: #e6e6e6;
}

[v-cloak] { display: none; }
</style>
