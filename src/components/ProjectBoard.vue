<template>
  <section class="board">
    <TaskColumn
      title="To Do"
      status="todo"
      :tasks="todoTasks"
      @edit-task="$emit('edit-task', $event)"
      @delete-task="$emit('delete-task', $event)"
      @change-status="$emit('change-status', $event)"
      @task-dropped="$emit('task-dropped', $event)"
    />

    <TaskColumn
      title="In Progress"
      status="progress"
      :tasks="progressTasks"
      @edit-task="$emit('edit-task', $event)"
      @delete-task="$emit('delete-task', $event)"
      @change-status="$emit('change-status', $event)"
      @task-dropped="$emit('task-dropped', $event)"
    />

    <TaskColumn
      title="Done"
      status="done"
      :tasks="doneTasks"
      @edit-task="$emit('edit-task', $event)"
      @delete-task="$emit('delete-task', $event)"
      @change-status="$emit('change-status', $event)"
      @task-dropped="$emit('task-dropped', $event)"
    />
  </section>
</template>

<script setup>
import { computed } from 'vue'
import TaskColumn from './TaskColumn.vue'

const props = defineProps({
  tasks: {
    type: Array,
    default: () => []
  }
})

defineEmits(['edit-task', 'delete-task', 'change-status', 'task-dropped'])

const todoTasks = computed(() => props.tasks.filter((task) => task.status === 'todo'))
const progressTasks = computed(() => props.tasks.filter((task) => task.status === 'progress'))
const doneTasks = computed(() => props.tasks.filter((task) => task.status === 'done'))
</script>
