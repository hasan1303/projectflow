<template>
  <div class="control-card grow">
    <label class="control-label">Search Tasks</label>
    <input
      class="control-input"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
      type="text"
      placeholder="Search by title, description, or label..."
    />
  </div>
</template>

<script setup>
defineProps({
  modelValue: {
    type: String,
    default: ''
  }
})

defineEmits(['update:modelValue'])
</script>
