<template>
  <section class="todoapp">
    <header class="header">
      <h1> Todos</h1>
      <input type="text" class="new-todo" placeholder="Ajouter une tache" v-model="newTodo" @keyup.enter="addTodo">
    </header>
    <div class="main">
      <input type="checkbox" class="toggle-all" id="toggle-all" v-model="allDone">
      <label for="toggle-all">Mark all as complete</label>  
      <ul class="todo-list">
        <li class="todo" v-for="todo in filteredTodos" :class="{completed : todo.completed, editing: todo == editing}">
          <div class="view">
            <input class="toggle" type="checkbox" v-model="todo.completed">
            <label @dblclick.prevent="editTodo(todo)"> {{ todo.name }}</label>
            <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
          </div>
          <!-- Input qui sert à éditer la tache -->
          <input type="text" class="edit" v-model="todo.name"  @keyup.enter="doneEdit" @keyup.esc="cancelEdit" v-focus="todo == editing">
        </li>
      </ul>

    </div>
    <footer class="footer" v-show="hasTodos">
      <span class="todo-count">
        <strong> {{ remaining }}</strong> Tâches à faire
      </span>
      <ul class="filters">
        <li><a href="#" :class="{selected: filter === 'all'}" @click.prevent="filter='all'">Toutes</a></li>
        <li><a href="#" :class="{selected: filter === 'todo'}" @click.prevent="filter='todo'">A faire</a></li>
        <li><a href="#" :class="{selected: filter === 'done'}" @click.prevent="filter ='done'">Faites</a></li>
      </ul>
      <button class="clear-completed" v-show="completed" @click.prevent="deleteCompleted"> Supprimer tâches finies</button>
    </footer>
    
  </section>


</template>

<script> 
export default {
  data: () => {
    return {
      todos: [{
        name: "Une tache de test",
        completed : false
      }],
      newTodo: '',
      filter: 'all',
      editing: null, 
      oldTodo: ''
    }
  },
  methods:{
    addTodo(){
      this.todos.push({
        completed: false,
        name: this.newTodo
      })
      // on vide le champ 
      this.newTodo = ""
    }, 
    deleteTodo(todo) {
      //on supprime la todo visée dans la boucle
      this.todos = this.todos.filter( i => i !== todo)
    }, 
    deleteCompleted(){
      // on supprime les todos qui ont completed a true 
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    editTodo(todo){
      // on assigne la todo a editing au moment du double click
      this.editing = todo
      this.oldTodo = todo.name
    }, 
    doneEdit(){
      this.editing = null
    }, 
    cancelEdit(){
      this.editing.name = this.oldTodo
      this.doneEdit()
    }
  }, 
  computed:{
    allDone: {
      get(value){
        // dans le cas ou toutes les taches sont finies ont passe remaining à 0
        return this.remaining === 0
      },
      set(value){
        if( value === true ) {
          this.todos.forEach( todo => {
            todo.completed = true
          })
        } else if ( value === false ) {
            this.todos.forEach( todo => {
            todo.completed = false
          })
        }
      }
    },
    hasTodos () {
      return this.todos.length > 0
    },
    remaining () {
      // On retourne le nombre de tâches non complétées
      return this.todos.filter(todo => !todo.completed).length
    },
    completed () {
      // On retourne le nombre de tâches non complétées
      return this.todos.filter(todo => todo.completed).length
    },
    filteredTodos() {
      if( this.filter === 'todo'){
        return this.todos.filter(todo => !todo.completed)
      } else if ( this.filter === 'done') {
        return this.todos.filter(todo => todo.completed)
      } 
      return this.todos
    }
  }, 
  directives: {
    focus (el, value) {
      if (value) {
        el.focus()
      }
    }
  }
}

</script>


<style src="./todos.css">
</style>
