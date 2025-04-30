<script setup>
import { computed, onBeforeMount, ref, watch } from "vue";
import originalTodos from "./data/todos";
import StatusFilter from "./components/StatusFilter.vue";
import TodoItem from "./components/TodoItem.vue";

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
        <TodoItem
          v-for="todo of visibleTodos"
          :key="todo.id"
          :todo="todo"
          @delete="todos.splice(todos.indexOf(todo), 1)"
        />
      </section>

      <!-- Hide the footer if there are no todos -->
      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count">{{ activeTodos.length }} items left</span>

        <!-- Active link should have the 'selected' class -->
        <StatusFilter :status="status" v-model="status" />

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
