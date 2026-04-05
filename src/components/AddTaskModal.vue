<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-card">
      <div class="modal-header">
        <h2>Add New Task</h2>
        <button class="icon-btn" @click="$emit('close')">✕</button>
      </div>

      <div class="form-grid">
        <div class="form-group full">
          <label>Title</label>
          <input v-model="form.title" type="text" placeholder="Task title" />
        </div>

        <div class="form-group full">
          <label>Description</label>
          <textarea v-model="form.description" rows="4" placeholder="Task description"></textarea>
        </div>

        <div class="form-group">
          <label>Priority</label>
          <select v-model="form.priority">
            <option>High</option>
            <option>Medium</option>
            <option>Low</option>
          </select>
        </div>

        <div class="form-group">
          <label>Due Date</label>
          <input v-model="form.dueDate" type="date" />
        </div>

        <div class="form-group full">
          <label>Status</label>
          <select v-model="form.status">
            <option value="todo">To Do</option>
            <option value="progress">In Progress</option>
            <option value="done">Done</option>
          </select>
        </div>

        <div class="form-group full">
          <label>Labels</label>
          <input
            v-model="labelsInput"
            type="text"
            placeholder="e.g. Design, Frontend, Bug"
          />
          <small class="helper-text">Separate labels with commas.</small>
        </div>
      </div>

      <div class="modal-actions">
        <button class="btn btn-secondary" @click="$emit('close')">Cancel</button>
        <button class="btn btn-primary" @click="handleSave">Save Task</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'

const emit = defineEmits(['close', 'save'])

const labelsInput = ref('')

const form = reactive({
  title: '',
  description: '',
  priority: 'High',
  dueDate: '',
  status: 'todo'
})

function handleSave() {
  if (!form.title.trim() || !form.description.trim() || !form.dueDate) return

  emit('save', {
    title: form.title.trim(),
    description: form.description.trim(),
    priority: form.priority,
    dueDate: form.dueDate,
    status: form.status,
    labels: labelsInput.value
      .split(',')
      .map((label) => label.trim())
      .filter(Boolean)
  })
}
</script>
