<template>
  <div class="control-card">
    <label class="control-label">Sort By</label>
    <select
      class="control-input"
      :value="modelValue"
      @change="$emit('update:modelValue', $event.target.value)"
    >
      <option value="newest">Newest First</option>
      <option value="oldest">Oldest First</option>
      <option value="due-soon">Due Date: Soonest</option>
      <option value="due-late">Due Date: Latest</option>
    </select>
  </div>
</template>

<script setup>
defineProps({
  modelValue: {
    type: String,
    default: 'newest'
  }
})

defineEmits(['update:modelValue'])
</script>
