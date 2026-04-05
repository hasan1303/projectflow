<template>
  <div class="toast-container">
    <div
      v-for="toast in toasts"
      :key="toast.id"
      class="toast"
    >
      <span>{{ toast.message }}</span>
      <button class="toast-close" @click="$emit('remove', toast.id)">✕</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  toasts: {
    type: Array,
    default: () => []
  }
})

defineEmits(['remove'])
</script>
