<template>
  <div
    :class="['column', { 'drag-over': isDragOver }]"
    @dragover.prevent="handleDragOver"
    @dragleave="handleDragLeave"
    @drop="handleDrop"
  >
    <div class="column-header">
      <div>
        <h2>{{ title }}</h2>
        <p class="column-subtitle">{{ columnText }}</p>
      </div>
      <span class="task-count">{{ tasks.length }}</span>
    </div>

    <div class="column-body">
      <TaskCard
        v-for="task in tasks"
        :key="task.id"
        :task="task"
        @edit-task="$emit('edit-task', $event)"
        @delete-task="$emit('delete-task', $event)"
        @change-status="$emit('change-status', $event)"
      />

      <div v-if="tasks.length === 0" class="empty-state">
        <div class="empty-icon">📂</div>
        <h3>No tasks in {{ title }}</h3>
        <p>Drag a task here or create a new one to keep the workflow moving.</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import TaskCard from './TaskCard.vue'

const props = defineProps({
  title: String,
  status: String,
  tasks: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['edit-task', 'delete-task', 'change-status', 'task-dropped'])

const isDragOver = ref(false)

const columnText = computed(() => {
  if (props.status === 'todo') return 'Tasks waiting to be started'
  if (props.status === 'progress') return 'Tasks currently being worked on'
  return 'Tasks already completed'
})

function handleDragOver() {
  isDragOver.value = true
}

function handleDragLeave() {
  isDragOver.value = false
}

function handleDrop(event) {
  isDragOver.value = false
  const taskId = Number(event.dataTransfer.getData('taskId'))
  if (!taskId) return

  emit('task-dropped', {
    taskId,
    newStatus: props.status
  })
}
</script>
