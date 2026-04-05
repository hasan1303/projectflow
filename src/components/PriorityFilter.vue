<template>
  <div class="control-card">
    <label class="control-label">Priority</label>
    <select
      class="control-input"
      :value="modelValue"
      @change="$emit('update:modelValue', $event.target.value)"
    >
      <option value="All">All</option>
      <option value="High">High</option>
      <option value="Medium">Medium</option>
      <option value="Low">Low</option>
    </select>
  </div>
</template>

<script setup>
defineProps({
  modelValue: {
    type: String,
    default: 'All'
  }
})

defineEmits(['update:modelValue'])
</script>
