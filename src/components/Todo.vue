<template>
  <div>
    <div class="card" v-show="isEditing == false">
      <div class="extra-button">
        <a href="javascript:void(0)" v-show="todo.status == 'Pending'" v-on:click="editTodo" class="edit-button"><i class="fa fa-pencil"></i></a>
        <a href="javascript:void(0)" v-on:click="deleteTodo(todo)" class="remove-button"><i class="fa fa-times"></i></a>
      </div>
      <div class="card-main">
        <div class="title">
          {{ todo.title }}
        </div>
        <div class="descrption">
          {{ todo.description}}
        </div>
      </div>
      <div class="card-button">
        <span v-show='todo.status == "Completed"' class="main-button">Completed</span>
        <button type="submit" v-show='todo.status == "Pending"' class="main-button" v-on:click="completeTask(todo)"><span class="a">Complete</span><span class="b">Pending</span></button>
      </div>
    </div>
    <div class="card" v-show="isEditing == true">
      <form class="edit-form" v-on:submit.prevent="">
        <div class="card-main">
          <div class="header">
            Title
          </div>
          <input type="text" name="description" v-model="todo.title">
          <div class="header">
            Description
          </div>
          <input type="text" name="description" v-model="todo.description">
        </div>
      <div class="card-button">
        <button class="main-button" v-on:click="doneEditing(todo)">Done</button>
      </div>
      </form>
    </div>
  </div>
</template>

<script>
import localforage from 'localforage/dist/localforage.js'
import sweetalert from 'sweetalert'

export default {
  props: [ 'todo' ],
  data() {
    return {
      isEditing: false
    }
  },
  methods: {
    editTodo() {
      this.isEditing = true
    },
    doneEditing(todo) {
      if(todo.title == "" || todo.description == "") {
        swal("Failed!", "Please fill-up all the inputs", "error")
      }
      else {
        swal("Success!", "Successfully edited a To-Do!", "success")
        this.$emit('edit-todo', todo)
      }
      this.isEditing = false
    },

    completeTask(todo) {
      todo.status = "Completed"
      this.$emit('complete-todo', todo)
    },

    deleteTodo(todo) {
      sweetalert({
        title: 'Are you sure?',
        text: 'This To-Do will be permanently deleted!',
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#ff8552',
        confirmButtonText: 'Yes, delete it!',
        closeOnConfirm: false,
      },
      () => {
        sweetalert('Deleted!', 'Your To-Do has been deleted.', 'success');
        this.$emit('delete-todo', todo)
      });
    }
  }
};
</script>
