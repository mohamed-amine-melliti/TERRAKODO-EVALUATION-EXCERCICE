<template>
  <article class="group flex flex-col bg-white dark:bg-dark-grey p-4 rounded-lg cursor-pointer shadow-task max-w-[280px]">
    <h3 class="text-black dark:text-white font-bold group-hover:text-main-purple select-none">{{ task.title }}</h3>
  </article>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  task: {
    type: Object,
    required: true
  }
})

const subtasksCompleted = computed(() => {
    const completed = props.task.subtasks.filter((sub) => sub.isCompleted).length;
  const total = props.task.subtasks.length;
  return `${completed} of ${total}`
})
</script>