<script>
  import { createTodo, deleteTodo, getTodos, updateTodo } from './api/todos';
import Message from './components/Message.vue';
  import StatusFilter from './components/StatusFilter.vue';
  import TodoItem from './components/TodoItem.vue';
  import todos from './data/todos';

  export default {
    components: {
      StatusFilter,
      TodoItem,
      Message,
    },
    data() {
      // let todos = [];
      // const jsonData = localStorage.getItem('todos') || '[]';

      // try {
      //   todos = JSON.parse(jsonData);
      // } catch (e) {}

      return {
        todos: [],
        title: '',
        status: 'all',
        errorMessage: '',
      }
    },
    computed: {
      activeTodos() {
        return this.todos.filter((todo) => !todo.completed);
      },
      completedTodos() {
        return this.todos.filter((todo) => todo.completed);
      },
      visibleTodos() {
        switch (this.status) {
          case 'active':
            return this.activeTodos;

          case 'completed':
            return this.completedTodos;
        
          default:
            return this.todos;
        }
      }
    },
    // watch: {
    //   todos: {
    //     deep: true,
    //     handler() {
    //       localStorage.setItem('todos', JSON.stringify(this.todos));
    //     },
    //   }
    // },
    mounted() {
      getTodos()
        .then(({ data }) => {
          this.todos = data;
        })
        .catch(() => {
          this.errorMessage = 'Unable to load todos';
        });
    },
    methods: {
      handleSubmit() {
        createTodo(this.title)
          .then(({ data }) => {
            this.todos = [...this.todos, data];
            this.title = '';
          })
          .catch(() => {
            this.errorMessage = 'Unable to add a todo';
          });
      },
      updateCurrentTodo({ id, title, completed }) {
        updateTodo({ id, title, completed })
          .then(({ data }) => {
            this.todos = this.todos.map(todo => todo.id === id ? data : todo)
          })
          .catch(() => {
            this.errorMessage = 'Unable to update a todo';
          });
      },
      deleteCurrentTodo(todoId) {
        deleteTodo(todoId)
          .then(() => {
            this.todos = this.todos.filter(todo => todo.id !== todoId);
          })
          .catch(() => {
            this.errorMessage = 'Unable to delete a todo';
          });
      },
      deleteCompletedTodos() {
        const todosToDelete = this.completedTodos.map(todo => deleteTodo(todo.id));

        Promise.all(todosToDelete)
          .then(() => this.todos = this.activeTodos)
          .catch(() => {
            this.errorMessage = 'Unable to delete a completed todos';
          });
      },
      toggleAllTodos() {
        const togglingTodos = todoIdsForToggle.map(id => updateTodo(id, data));
      },
    }
  }
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          type="button"
          class="todoapp__toggle-all"
          :class="{ active: activeTodos.length === 0 }"
        ></button>

        <form @submit.prevent="handleSubmit">
          <input
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <TransitionGroup 
        name="todo-list"  
        tag="section" 
        class="todoapp__main"
      >
        <TodoItem
          v-for="todo, index of visibleTodos"
          :key="todo.id"
          :todo="todo"
          @update="updateCurrentTodo"
          @delete="deleteCurrentTodo(todo.id)"
        />
      </TransitionGroup>

      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" data-cy="TodosCounter">
          {{ activeTodos.length }} items left
        </span>

        <!-- <StatusFilter :value="status" @change="status = $event"/> -->
        <StatusFilter v-model="status" />

        <button
          v-if="completedTodos.length > 0"
          type="button"
          class="todoapp__clear-completed"
          @click="deleteCompletedTodos"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <Message 
      class="is-danger" 
      :active="errorMessage !== ''"
      @hide="errorMessage = ''"
    >
      <template #default>
        <p>{{ errorMessage }}</p>
      </template>

      <template #header>
        <p>Server Error</p>
      </template>
    </Message>
  </div>
</template>

<style>
  .todo-list-enter-active,
  .todo-list-leave-active {
    max-height: 60px;
    transition: all 0.5s ease;
  }
  .todo-list-enter-from,
  .todo-list-leave-to {
    opacity: 0;
    max-height: 0;
    transform: scaleY(0);
  }
</style>
