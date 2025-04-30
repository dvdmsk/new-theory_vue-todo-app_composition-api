<script setup>
  import { defineProps, defineEmits } from 'vue';

  const { todo } = defineProps(['todo']);
  const emit = defineEmits(['delete']);
</script>

<template>
  <div class="todo" :class="{ completed: todo.completed }">
    <label class="todo__status-label">
      <input type="checkbox" class="todo__status" v-model="todo.completed" />
    </label>

    <!-- show when todo is being edited -->
    <form v-if="false">
      <input
        class="todo__title-field"
        placeholder="Empty todo will be deleted"
      />
    </form>

    <template v-else>
      <span class="todo__title">{{ todo.title }}</span>
      <button class="todo__remove" @click="emit('delete')">×</button>
    </template>

    <!-- add `is-active` class when todo being processed -->
    <div class="modal overlay" :class="{ 'is-active': false }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>
