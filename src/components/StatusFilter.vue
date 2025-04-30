<script setup>
import { defineProps, defineEmits } from "vue";

const emit = defineEmits(["change"]);

const { status } = defineProps({
  status: String,
});
</script>

<template>
  <nav class="filter">
    <a
      href="#/"
      class="filter__link"
      :class="{ selected: status === 'all' }"
      @click.prevent="emit('change', 'all')"
    >
      All
    </a>
    <a
      href="#/active"
      class="filter__link"
      :class="{ selected: status === 'active' }"
      @click.prevent="emit('change', 'active')"
    >
      Active
    </a>
    <a
      href="#/completed"
      class="filter__link"
      :class="{ selected: status === 'completed' }"
      @click.prevent="emit('change', 'completed')"
    >
      Completed
    </a>
  </nav>
</template>
