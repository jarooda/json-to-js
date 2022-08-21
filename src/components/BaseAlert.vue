<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  variant: String
})

const alertClasses = computed(() => {
  return {
    'base-alert': true,
    [`variant-${props.variant}`]: true
  }
})
</script>

<template>
  <div :class="alertClasses">
    <slot />
  </div>
</template>

<style scoped>
.base-alert {
  padding: 16px 32px;
  margin: 0 auto;
  width: fit-content;
  border-radius: 10px;
  margin-bottom: 16px;
  text-align: center;
  font-size: 12px;
}

.variant-success {
  background-color: #199c0b;
  color: var(--vt-c-white);
}

.variant-error {
  background-color: #d92b2b;
  color: var(--vt-c-black);
}
</style>