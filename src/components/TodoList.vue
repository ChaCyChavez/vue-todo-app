<template>
  <div id="todo-list">
    <div id="pending-todo">
      <div class="todo-title">
        Pending : {{ db.todos.filter(todo => {return todo.status == "Pending"}).length }}
      </div>
      <todo v-on:complete-todo="completeTodo" v-on:delete-todo="deleteTodo" v-on:edit-todo="editTodo" v-for='todo in db.todos.filter(todo => {return todo.status == "Pending"})' v-bind:todo="todo"></todo>
    </div>
    <div id="completed-todo">
      <div class="todo-title">
        Completed : {{ db.todos.filter(todo => {return todo.status == "Completed"}).length }}
      </div>
      <todo v-on:delete-todo="deleteTodo" v-for='todo in db.todos.filter(todo => {return todo.status == "Completed"})' v-bind:todo="todo"></todo>
    </div>
  </div>
	
</template>


<script>
import Todo from './Todo.vue'
import localforage from 'localforage/dist/localforage.js'

export default {
  props: [ 'db' ],
  components: { Todo, },
  data() {
    return {
      title: "Todos"
    }
  },
  methods: {
    deleteTodo(todo) {
        const index = this.db.todos.indexOf(todo)
        this.db.todos.splice(index, 1)

        localforage.removeItem(todo.id)
        .then(function() {
          console.log("Successfully deleted a to-do")
        })
        .catch(function(err) {
          console.log(err)
        })
    },

    editTodo(todo) {
      localforage.setItem(todo.id, todo)
      .then(function() {
        console.log("Successfully edited a to-do")
      })
      .catch(function(err) {
        console.log(err)
      })
    },

    completeTodo(todo) {
      localforage.setItem(todo.id, todo)
      .then(function() {
        console.log("Successfully")
      })
      .catch(function(err) {
        console.log(err)
      })
    }
  }
};
</script>