<template>
  <section class="todoapp">
    <header class="header">
      <h1>Todos</h1>
      <input type="text" class="new-todo" placeholder="Ajouter une tâche" v-model="newTodo" @keyup.enter="addTodo">
    </header>
    <div class="main">
      <template v-if="hasTodo">
        <input id="toggle-all" type="checkbox" class="toggle-all" v-model="allDone">
        <label for="toggle-all">Mark all as complete</label>
      </template>
      <ul class="todo-list">
        <li class="todo" v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: todo === editing}" @dblclick="startEdition(todo)">
          <div class="view">
            <input type="checkbox" v-model="todo.completed" class="toggle">
            <label>{{ todo.name }}</label>
            <button class="destroy" @click="deleteTodo(todo)"></button>
          </div>
          <input ref="edition" type="text" class="edit" v-show="editing === todo" v-model="todo.name" v-focus="todo === editing" @keyup.enter="doneEdition" @keyup.escape="doneEdition" @keyup.tab="doneEdition" @blur="doneEdition">
        </li>
      </ul>
    </div>
    <footer class="footer" v-show="hasTodo">
      <span class="todo-count"><strong>{{ remaining.length }}</strong> tâche<template v-if="remaining.length > 1">s</template> à faire</span>
      <ul class="filters">
        <li v-for="f in filters">
          <a href="#" :class="{selected: filter === f.id}" @click.prevent="filter = f.id">{{ f.label }}</a>
        </li>
      </ul>
      <button v-show="hasCompleted" class="clear-completed" @click="clearCompleted">Supprimer les tâches finies</button>
    </footer>
  </section>
</template>

<script>
export default {
  data () {
    return {
      todos: [],
      newTodo: '',
      filters: [{
        id: 'all',
        label: 'Toutes',
        action: () => this.todos
      }, {
        id: 'todo',
        label: 'À faire',
        action: () => this.remaining
      }, {
        id: 'done',
        label: 'Faites',
        action: () => this.complete
      }],
      filter: 'all',
      editing: null,
      edition: ''
    }
  },
  computed: {
    remaining () {
      return this.todos.filter(todo => !todo.completed)
    },
    complete () {
      return this.todos.filter(todo => todo.completed)
    },
    filteredTodos () {
      return this.filters.find(f => f.id === this.filter).action()
    },
    allDone: {
      get () {
        return this.remaining === 0
      },
      set (value) {
        this.todos.forEach(todo => todo.completed = value)
      }
    },
    hasTodo () {
      return this.todos.length > 0
    },
    hasCompleted () {
      return this.complete.length > 0
    }
  },
  methods: {
    addTodo () {
      this.todos.push({
        name: this.newTodo,
        completed: false
      })
      this.newTodo = ''
    },
    deleteTodo (todo) {
      this.todos = this.todos.filter(t => t !== todo)
    },
    clearCompleted () {
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    startEdition (todo) {
      this.editing = todo
    },
    doneEdition () {
      this.editing = null
    }
  },
  directives: {
    focus (el) {
      el.focus()
    }
  },
  mounted () {
    if (localStorage.todos) {
      this.todos = JSON.parse(localStorage.todos)
    }
  },
  watch: {
    todos: {
      handler () {
        localStorage.todos = JSON.stringify(this.todos)
      },
      deep: true
    }
  }
}
</script>

<style src="./todos.css"></style>
