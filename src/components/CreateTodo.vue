<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">
              Create Todo
            </slot>
          </div>

          <form v-on:submit.prevent="">
            <div class="modal-body">
              <div name="body">
                <div>Title</div>
                <input v-model="newTodo.title">
                <div>Description</div>
                <input v-model="newTodo.description">
              </div>
            </div>

            <div class="modal-footer">
              <div name="footer">
                <button type="submit" class="modal-default-button" v-on:click="createTodo(db)">
                  OK
                </button>
                <button class="modal-default-button" v-on:click="closeModal">
                  Cancel
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import localforage from 'localforage/dist/localforage.js'
import sweetalert from 'sweetalert'

export default{
  props: [ 'db' ],
  data() {
    return {
      newTodo: {  
        id: "",
        title: "",
        description: "",
        status: "Pending"
      }
    }
  },
  methods: {
    closeModal() {
      this.$emit('close')
    },
    
    createTodo() {
      if(this.newTodo.title == "" || this.newTodo.description == "") {
        swal("Failed!", "Please fill-up all the inputs", "error");
      }
      else {
        swal("Success!", "Successfully added a To-Do!", "success");
        this.$emit('create-todo', this.newTodo, this.db)
      }
      this.closeModal()
    }
  }
}
</script>