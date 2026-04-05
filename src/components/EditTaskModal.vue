<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-card">
      <div class="modal-header">
        <h2>Edit Task</h2>
        <button class="icon-btn" @click="$emit('close')">✕</button>
      </div>

      <div class="form-grid">
        <div class="form-group full">
          <label>Title</label>
          <input v-model="localTask.title" type="text" />
        </div>

        <div class="form-group full">
          <label>Description</label>
          <textarea v-model="localTask.description" rows="4"></textarea>
        </div>

        <div class="form-group">
          <label>Priority</label>
          <select v-model="localTask.priority">
            <option>High</option>
            <option>Medium</option>
            <option>Low</option>
          </select>
        </div>

        <div class="form-group">
          <label>Due Date</label>
          <input v-model="localTask.dueDate" type="date" />
        </div>

        <div class="form-group full">
          <label>Status</label>
          <select v-model="localTask.status">
            <option value="todo">To Do</option>
            <option value="progress">In Progress</option>
            <option value="done">Done</option>
          </select>
        </div>

        <div class="form-group full">
          <label>Labels</label>
          <input v-model="labelsInput" type="text" />
          <small class="helper-text">Separate labels with commas.</small>
        </div>
      </div>

      <div class="modal-actions">
        <button class="btn btn-secondary" @click="$emit('close')">Cancel</button>
        <button class="btn btn-primary" @click="handleUpdate">Update Task</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, watch } from 'vue'

const props = defineProps({
  task: Object
})

const emit = defineEmits(['close', 'save'])

const labelsInput = ref('')

const localTask = reactive({
  id: null,
  title: '',
  description: '',
  priority: 'Medium',
  dueDate: '',
  status: 'todo',
  labels: []
})

watch(
  () => props.task,
  (newTask) => {
    if (newTask) {
      localTask.id = newTask.id
      localTask.title = newTask.title
      localTask.description = newTask.description
      localTask.priority = newTask.priority
      localTask.dueDate = newTask.dueDate
      localTask.status = newTask.status
      localTask.labels = [...(newTask.labels || [])]
      labelsInput.value = (newTask.labels || []).join(', ')
    }
  },
  { immediate: true }
)

function handleUpdate() {
  emit('save', {
    ...localTask,
    title: localTask.title.trim(),
    description: localTask.description.trim(),
    labels: labelsInput.value
      .split(',')
      .map((label) => label.trim())
      .filter(Boolean)
  })
}
</script>
