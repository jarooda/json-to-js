<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  disabled: Boolean,
  block: Boolean
})

const emit = defineEmits(['click'])

const buttonClasses = computed(() => {
  return {
    block: props.block,
    'base-button': true
  }
})
</script>

<template>
  <button
    :class="buttonClasses"
    @click="emit('click')"
    v-bind="props"
  >
    <slot />
  </button>
</template>

<style scoped>
.base-button {
  cursor: pointer;
  border: none;
  border-radius: 30px;
  padding: 8px 16px;
  margin: 0;
  width: fit-content;
  background-color: var(--vt-c-indigo);
  color: var(--vt-c-white-soft);
  font-weight: 600;
}

.block {
  width: 100%;
}

.base-button:disabled, .base-button:disabled:hover {
  background-color: var(--vt-c-black-mute);
  color: var(--vt-c-white-soft);
}

.base-button:hover {
  color: var(--vt-c-indigo);
  background-color: var(--vt-c-white-soft);
}
</style>