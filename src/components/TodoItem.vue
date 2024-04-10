<script>
  export default {
    name: 'TodoItem',
    props: {
      todo: Object,
    },
    emits: ['update', 'delete'],
    data() {
      return {
        editing: false,
        newTitle: this.todo.title,
      };
    },
    methods: {
      toggleCompleted() {
        this.$emit('update', {
          ...this.todo,
          completed: !this.todo.completed,
        });
      },
      deleteTodo() {
        this.$emit('delete');
      },
      renameTodo() {
        if (!this.editing) {
          return;
        }

        this.editing = false;

        if (this.newTitle === this.todo.title) {
          return;
        }

        if (this.newTitle === '') {
          this.deleteTodo()

          return;
        }

        this.$emit('update', {
          ...this.todo,
          title: this.newTitle,
        });

        // this.newTitle = '';
        
      },
      edit() {
        this.newTitle = this.todo.title;
        this.editing = true;

        this.$nextTick(() => {
          this.$refs['title-field'].focus();
        });
      }
    }
  }
</script>

<template>
  <div 
    class="todo"
    :class="{ completed: todo.completed }"
  >
    <label class="todo__status-label">
      <input
        type="checkbox"
        class="todo__status"
        :checked="todo.completed"
        @change="toggleCompleted"
      />
    </label>

    <form v-if="editing" @submit.prevent="renameTodo">
      <input
        type="text"
        class="todo__title-field"
        placeholder="Empty todo will be deleted"
        v-model.trim="newTitle"
        ref="title-field"
        @keyup.esc="editing = false"
        @blur="renameTodo"
      />
    </form>

    <template v-else>
      <span class="todo__title" @dblclick="edit">
        {{ todo.title }}
      </span>
      <button 
        type="button" 
        class="todo__remove"
        @click="deleteTodo"
      >×</button>
    </template>

    <div class="modal overlay" :class="{ 'is-active': false }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>

<style>
</style>