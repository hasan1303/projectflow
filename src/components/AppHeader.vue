<template>
  <header class="app-header">
    <div>
      <p class="eyebrow">Project Management App</p>
      <h1>Modern Kanban Dashboard</h1>
      <p class="header-subtext">
        {{ taskCount }} tasks across your selected project
      </p>
    </div>

    <div class="header-actions">
      <button class="btn btn-secondary" @click="$emit('open-project-modal')">
        + Project
      </button>
      <button class="btn btn-primary" @click="$emit('open-task-modal')">
        + Task
      </button>
      <button class="theme-toggle" @click="$emit('toggle-dark')">
        {{ darkMode ? '☀ Light' : '🌙 Dark' }}
      </button>
    </div>
  </header>
</template>

<script setup>
defineProps({
  darkMode: Boolean,
  taskCount: Number
})

defineEmits(['toggle-dark', 'open-project-modal', 'open-task-modal'])
</script>
