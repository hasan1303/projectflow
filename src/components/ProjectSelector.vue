<template>
  <div class="control-card">
    <label class="control-label">Select Project</label>
    <select
      class="control-input"
      :value="selectedProjectId"
      @change="$emit('select-project', Number($event.target.value))"
    >
      <option
        v-for="project in projects"
        :key="project.id"
        :value="project.id"
      >
        {{ project.name }}
      </option>
    </select>
  </div>
</template>

<script setup>
defineProps({
  projects: {
    type: Array,
    default: () => []
  },
  selectedProjectId: Number
})

defineEmits(['select-project'])
</script>
