<template>
  <article
    class="task-card"
    :class="{ overdue: isOverdue }"
    draggable="true"
    @dragstart="handleDragStart"
  >
    <div class="task-top">
      <span :class="['priority-badge', task.priority.toLowerCase()]">
        {{ task.priority }}
      </span>

      <span :class="['due-date', { overdue: isOverdue }]">
        {{ dueLabel }}
      </span>
    </div>

    <h3>{{ task.title }}</h3>
    <p>{{ task.description }}</p>

    <div v-if="task.labels?.length" class="task-labels">
      <span
        v-for="label in task.labels"
        :key="label"
        class="task-label"
      >
        {{ label }}
      </span>
    </div>

    <div class="task-footer">
      <span class="status-chip">{{ statusLabel }}</span>
    </div>

    <div class="task-actions">
      <div class="status-actions">
        <button
          v-if="task.status !== 'todo'"
          class="mini-btn"
          @click="$emit('change-status', { taskId: task.id, status: 'todo' })"
        >
          To Do
        </button>

        <button
          v-if="task.status !== 'progress'"
          class="mini-btn"
          @click="$emit('change-status', { taskId: task.id, status: 'progress' })"
        >
          Progress
        </button>

        <button
          v-if="task.status !== 'done'"
          class="mini-btn"
          @click="$emit('change-status', { taskId: task.id, status: 'done' })"
        >
          Done
        </button>
      </div>

      <div class="card-controls">
        <button class="mini-btn edit" @click="$emit('edit-task', task)">Edit</button>
        <button class="mini-btn danger" @click="$emit('delete-task', task)">Delete</button>
      </div>
    </div>
  </article>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  task: {
    type: Object,
    required: true
  }
})

defineEmits(['edit-task', 'delete-task', 'change-status'])

const isOverdue = computed(() => {
  if (!props.task?.dueDate || props.task.status === 'done') return false

  const today = new Date()
  today.setHours(0, 0, 0, 0)

  const due = new Date(props.task.dueDate)
  due.setHours(0, 0, 0, 0)

  return due < today
})

const dueLabel = computed(() => {
  return isOverdue.value ? `Overdue: ${props.task.dueDate}` : `Due: ${props.task.dueDate}`
})

const statusLabel = computed(() => {
  if (props.task.status === 'todo') return 'To Do'
  if (props.task.status === 'progress') return 'In Progress'
  return 'Done'
})

function handleDragStart(event) {
  event.dataTransfer.setData('taskId', props.task.id)
  event.dataTransfer.effectAllowed = 'move'
}
</script>
