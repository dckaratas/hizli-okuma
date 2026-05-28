<template>
  <div class="pills">
    <button
      v-for="cat in categories"
      :key="cat"
      :class="['pill', modelValue === cat ? 'active' : '']"
      @click="$emit('update:modelValue', cat)"
    >{{ cat }}</button>
  </div>
</template>

<script setup>
defineProps({ categories: Array, modelValue: String })
defineEmits(['update:modelValue'])
</script>

<style scoped>
.pills {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
  margin-bottom: 1.25rem;
}
.pill {
  font-size: 12px;
  font-family: var(--mono);
  padding: 4px 12px;
  border-radius: 20px;
  border: 1px solid var(--border-light);
  color: var(--text-secondary);
  background: transparent;
  transition: all 0.15s;
  letter-spacing: 0.01em;
}
.pill:hover {
  border-color: var(--accent);
  color: var(--accent);
  background: var(--accent-bg);
}
.pill.active {
  border-color: var(--accent);
  color: var(--accent);
  background: var(--accent-bg);
}
</style>
