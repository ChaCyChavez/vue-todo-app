<template>
  <div id="app">
    <div id="app-header">
    	<div id="app-title">
      	{{ title }}
      </div>
      <div id="search">
      	<form v-on:submit.prevent="search(db, keySearch)">
          <input type="search" placeholder="Search Todo" v-model="keySearch"/>
        </form>
      </div>
    </div>
    <div id="app-body">
      <todo-list v-bind:db="db"></todo-list>
    </div>
    <a href="javascript:void(0)" id="create-button" v-on:click="openModal"><i class="fa fa-plus fa-2x"></i></a>
    <!-- use the modal component, pass in the prop -->
    <create-todo v-on:create-todo="createTodo" v-bind:db="db" v-if="isCreating" v-on:close="closeModal"></create-todo>
  </div>
</template>

<script>
import TodoList from './components/TodoList.vue'
import CreateTodo from './components/CreateTodo.vue'
import localforage from 'localforage/dist/localforage.js'

export default {
  components: { TodoList, CreateTodo},

  data() {
    return {
      title: '// TODO: App',
      keySearch: "",
      isCreating: false,
      db: {
        todos: [],
        counter: 0
      }
    }
  },
  methods: {
    openModal() {
      this.isCreating = true
    },

    createTodo(newTodo, db) {
      newTodo.id = db.counter.toString()
      
      db.todos.push(newTodo)
      localforage.setItem(newTodo.id, newTodo)
      .then(function() {
        console.log(localforage)
        db.counter++
        localforage.setItem('counter', db.counter)
        .then(() => {
          console.log("succesfully set counter to " + db.counter)
        })
        .catch(function(err) {
          console.log(err)
        })
      })
      .catch(function(err) {
        console.log(err)
      })
    },

    closeModal() {
      this.isCreating = false
    },

    initTodo(db) {
      localforage.getItem('counter')
      .then(function(value) {
        db.counter = value
        if(db.counter == null) {
          db.counter = 0
          localforage.setItem('counter', 0)
          .then(function() {
            console.log("Successfully set counter to 0")
          })
          .catch(function() {
            console.log(err)
          })
        }
      })
      .catch(function(err) {
        console.log(err)
      })

      localforage.iterate(function(value, key, iterationNumber) {
        if(key != 'counter'){
          db.todos.push(value)
        }
      }).then(function() {
          console.log("Successfully retrieve all to-do's")
      }).catch(function(err) {
          console.log(err)
      })
    },
    
    search(db, keySearch) {
      db.todos.splice(0, db.todos.length);
      if(keySearch == "") {
        this.initTodo(db)
      }
      else {
        localforage.iterate(function(value, key, iterationNumber) {
          if(key != 'counter' && value.title.toLowerCase().search(keySearch.toLowerCase()) >= 0) {
            db.todos.push(value)
          }
        }).then(function() {
            console.log("Successfully retrieve to-do")
        }).catch(function(err) {
            console.log(err)
        })
      }
    }
  },
  mounted() {
    this.initTodo(this.db)
  }
}
</script>
