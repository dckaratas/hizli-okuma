<template>
  <div class="controls">
    <div class="control-card">
      <div class="control-label">
        <span class="label-text">Okuma hızı</span>
        <span class="label-value">{{ speed }} <small>kelime/dk</small></span>
      </div>
      <input type="range" min="20" max="1200" step="5"
        :value="speed" @input="$emit('update:speed', +$event.target.value)" />
      <div class="presets">
        <button
          v-for="p in speedPresets" :key="p.wpm"
          :class="['preset-btn', speed === p.wpm ? 'active' : '']"
          @click="$emit('update:speed', p.wpm)"
        >{{ p.label }}</button>
      </div>
    </div>

    <div class="control-card">
      <div class="control-label">
        <span class="label-text">Yazı boyutu</span>
        <span class="label-value">{{ fontSize }}<small>px</small></span>
      </div>
      <input type="range" min="14" max="30" step="1"
        :value="fontSize" @input="$emit('update:fontSize', +$event.target.value)" />
    </div>
  </div>
</template>

<script setup>
defineProps({ speed: Number, fontSize: Number })
defineEmits(['update:speed', 'update:fontSize'])

const speedPresets = [
  { label: 'Yavaş', wpm: 120 },
  { label: 'Orta', wpm: 280 },
  { label: 'Ortalama Üzeri', wpm: 500 },
  { label: 'Dünya Ortalaması Üzeri', wpm: 800 },
  { label: 'Üst Düzey', wpm: 1200 }
]
</script>

<style scoped>
.controls {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-bottom: 1.25rem;
}
.control-card {
  background: var(--bg-secondary);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: 14px 16px;
}
.control-label {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 12px;
}
.label-text {
  font-size: 12px;
  color: var(--text-secondary);
  text-transform: uppercase;
  letter-spacing: 0.06em;
  font-weight: 500;
}
.label-value {
  font-family: var(--mono);
  font-size: 15px;
  font-weight: 500;
  color: var(--accent);
}
.label-value small {
  font-size: 11px;
  color: var(--text-secondary);
  margin-left: 3px;
}
.presets {
  display: flex;
  gap: 5px;
  margin-top: 12px;
  flex-wrap: wrap;
}
.preset-btn {
  font-size: 11px;
  font-family: var(--mono);
  padding: 3px 9px;
  border-radius: 20px;
  border: 1px solid var(--border-light);
  color: var(--text-secondary);
  transition: all 0.15s;
}
.preset-btn:hover {
  border-color: var(--accent);
  color: var(--accent);
  background: var(--accent-bg);
}
.preset-btn.active {
  border-color: var(--accent);
  color: var(--accent);
  background: var(--accent-bg);
}
</style>
