<script>
  import todos from './data/todos';

  export default {
    data() {
      return {
        todos,
      }
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
          class="todoapp__toggle-all active"
        ></button>

        <form>
          <input
            data-cy="NewTodoField"
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
          />
        </form>
      </header>

      <section class="todoapp__main">
        <div 
          v-for="todo, index of todos"
          :key="todo.id"
          class="todo"
          :class="{ completed: todo.completed }"
        >
          <label class="todo__status-label">
            <input
              data-cy="TodoStatus"
              type="checkbox"
              class="todo__status"
              :checked="todo.completed"
            />
          </label>

          <form v-if="false">
            <input
              type="text"
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
              value="Todo is being edited now"
            />
          </form>

          <template v-else>
            <span class="todo__title">{{ todo.title }}</span>
            <button 
              type="button" 
              class="todo__remove"
              v-on:click="todos.splice(index, 1)"
            >×</button>
          </template>

          <div class="modal overlay" :class="{ 'is-active': false }">
            <div class="modal-background has-background-white-ter"></div>
            <div class="loader"></div>
          </div>
        </div>
      </section>

      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" data-cy="TodosCounter">
          3 items left
        </span>

        <nav class="filter" data-cy="Filter">
          <a
            href="#/"
            class="filter__link selected"
            data-cy="FilterLinkAll"
          >
            All
          </a>

          <a
            href="#/active"
            class="filter__link"
            data-cy="FilterLinkActive"
          >
            Active
          </a>

          <a
            href="#/completed"
            class="filter__link"
            data-cy="FilterLinkCompleted"
          >
            Completed
          </a>
        </nav>

        <button
          type="button"
          class="todoapp__clear-completed"
          data-cy="ClearCompletedButton"
        >
          Clear completed
        </button>
      </footer>
    </div>
  </div>
</template>

<style>
</style>