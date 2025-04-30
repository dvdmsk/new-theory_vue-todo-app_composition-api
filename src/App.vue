<script setup>
import { computed, onBeforeMount, ref, watch } from "vue";
import originalTodos from "./data/todos";
import StatusFilter from "./components/StatusFilter.vue";

const todos = ref([]);

onBeforeMount(() => {
  try {
    todos.value = JSON.parse(localStorage.getItem("todos"));
  } catch (error) {}

  if (!Array.isArray(todos.value)) {
    todos.value = [];
  }
});

const title = ref("");
function addTodo() {
  if (!title.value) {
    errorMessage.value = "Title should not be empty";

    return;
  }

  todos.value.push({
    id: Date.now(),
    title: title.value,
    completed: false,
  });

  title.value = "";
}

const errorMessage = ref("");
const activeTodos = computed(() =>
  todos.value.filter((todo) => !todo.completed)
);
const status = ref("all");

const visibleTodos = computed(() => {
  if (status.value === "active") {
    return activeTodos.value;
  }

  if (status.value === "completed") {
    return todos.value.filter((todo) => todo.completed);
  }

  return todos.value;
});

watch(
  todos,
  (newTodos) => {
    localStorage.setItem("todos", JSON.stringify(newTodos));
  },
  { deep: true }
);
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <!-- this button should have `active` class only if all todos are completed -->
        <button
          type="button"
          class="todoapp__toggle-all active"
          :class="{ active: activeTodos.length === 0 }"
        ></button>

        <!-- Add a todo on form submit -->
        <form @submit.prevent="addTodo">
          <input
            data-cy="NewTodoField"
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
            @input="errorMessage = ''"
          />
        </form>
      </header>

      <section class="todoapp__main" data-cy="TodoList">
        <div
          v-for="(todo, i) of visibleTodos"
          :key="todo.id"
          class="todo"
          :class="{ completed: todo.completed }"
        >
          <label class="todo__status-label">
            <input
              type="checkbox"
              class="todo__status"
              :checked="todo.completed"
              @change="todo.completed = !todo.completed"
            />
          </label>

          <form v-if="false">
            <input
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
            />
          </form>

          <template v-else>
            <span class="todo__title">{{ todo.title }}</span>
            <button class="todo__remove" @click="todos.splice(i, 1)">×</button>
          </template>

          <div class="modal overlay" :class="{ 'is-active': false }">
            <div class="modal-background has-background-white-ter"></div>
            <div class="loader"></div>
          </div>
        </div>
      </section>

      <!-- Hide the footer if there are no todos -->
      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count">{{ activeTodos.length }} items left</span>

        <!-- Active link should have the 'selected' class -->
        <StatusFilter :status="status" @change="status = $event" />

        <!-- this button should be disabled if there are no completed todos -->
        <button
          class="todoapp__clear-completed"
          :disabled="todos.length === activeTodos.length"
          @click="todos = activeTodos"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <!-- DON'T use conditional rendering to hide the notification -->
    <!-- Add the 'hidden' class to hide the message smoothly -->

    <div
      v-if="errorMessage"
      class="notification is-danger is-light has-text-weight-normal"
    >
      <button class="delete" @click="errorMessage = ''"></button>

      {{ errorMessage }}
    </div>
  </div>
</template>
