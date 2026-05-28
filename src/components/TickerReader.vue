<template>
  <div>
    <div class="ticker-wrap">
      <div class="mask left"></div>
      <div class="mask right"></div>
      <div ref="trackRef" class="track" :style="{ transform: `translateX(${Math.round(trackX)}px)` }">
        <span v-for="(word, i) in words" :key="i"
          :class="['word', wordClass(i)]"
          :style="{ fontSize: fontSize + 'px' }">{{ word }}</span>
        <span class="word" :style="{ fontSize: fontSize + 'px', opacity: 0, userSelect: 'none' }">　　　　　</span>
      </div>
    </div>

    <div class="para-info">
      <span class="tag-badge">{{ currentPara.tag }}</span>
      <span class="para-preview">{{ currentPara.text.slice(0, 48) }}…</span>
      <span class="para-num">{{ currentParaIdx + 1 }} / {{ paragraphs.length }}</span>
    </div>

    <div class="progress-bar">
      <div class="progress-fill" :style="{ width: progress + '%' }"></div>
    </div>

    <div class="btn-row">
      <button class="btn primary" @click="togglePlay">
        <span class="btn-icon">{{ isPlaying ? '⏸' : '▶' }}</span>
        {{ isPlaying ? 'Duraklat' : 'Başlat' }}
      </button>
      <button class="btn" @click="reset">
        <span class="btn-icon">↺</span> Sıfırla
      </button>
      <div class="stats">
        <div class="stat">
          <div class="stat-val">{{ speed }}</div>
          <div class="stat-lbl">kelime/dk</div>
        </div>
        <div class="stat">
          <div class="stat-val">{{ progress }}<small>%</small></div>
          <div class="stat-lbl">tamamlandı</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onUnmounted } from 'vue'

const props = defineProps({ paragraphs: Array, speed: Number, fontSize: Number })

const trackX = ref(700)
const isPlaying = ref(false)
const currentParaIdx = ref(0)
const currentWordIdx = ref(0)
const trackRef = ref(null)

const currentPara = computed(() => props.paragraphs[currentParaIdx.value] || props.paragraphs[0])
const words = computed(() => currentPara.value?.text.split(' ') ?? [])
const progress = computed(() => Math.round((currentParaIdx.value / props.paragraphs.length) * 100))
const pxPerSec = computed(() => (props.speed / 60) * 70)

let animFrame = null
let lastTime = null

function animate(ts) {
  if (!lastTime) lastTime = ts
  const dt = (ts - lastTime) / 1000
  lastTime = ts
  trackX.value -= pxPerSec.value * dt

  if (trackRef.value) {
    const center = 340
    const wordEls = trackRef.value.querySelectorAll('.word')
    let closest = 0, minDist = Infinity
    wordEls.forEach((w, i) => {
      const wx = trackX.value + w.offsetLeft + w.offsetWidth / 2
      const dist = Math.abs(wx - center)
      if (dist < minDist) { minDist = dist; closest = i }
    })
    currentWordIdx.value = closest

    const trackW = trackRef.value.scrollWidth
    if (trackX.value + trackW < 0) {
      if (currentParaIdx.value < props.paragraphs.length - 1) {
        currentParaIdx.value++; trackX.value = 700; lastTime = null
      } else { stop(); return }
    }
  }
  animFrame = requestAnimationFrame(animate)
}

function start() { isPlaying.value = true; lastTime = null; animFrame = requestAnimationFrame(animate) }
function stop() { isPlaying.value = false; if (animFrame) cancelAnimationFrame(animFrame); animFrame = null }
function togglePlay() { isPlaying.value ? stop() : start() }
function reset() { stop(); trackX.value = 700; currentParaIdx.value = 0; currentWordIdx.value = 0 }
function wordClass(i) {
  const diff = i - currentWordIdx.value
  if (diff === 0) return 'highlight'
  if (diff < 0) return 'dim'
  return ''
}

watch(() => props.paragraphs, reset)
onUnmounted(stop)
</script>

<style scoped>
.ticker-wrap {
  position: relative; height: 90px;
  border: 1px solid var(--border); border-radius: var(--r-lg);
  background: var(--bg); overflow: hidden;
  display: flex; align-items: center; margin-bottom: 10px;
}
.mask { position: absolute; top: 0; bottom: 0; width: 100px; z-index: 2; pointer-events: none; }
.mask.left  { left: 0;  background: linear-gradient(to right, var(--bg), transparent); }
.mask.right { right: 0; background: linear-gradient(to left,  var(--bg), transparent); }
.track { position: absolute; white-space: nowrap; display: flex; align-items: center; }
.word { display: inline-block; padding: 0 5px; line-height: 1.4; color: var(--text); transition: color 0.08s; }
.word.highlight { color: var(--accent); font-weight: 600; }
.word.dim { color: var(--text-dim); }

.para-info { display: flex; align-items: center; gap: 8px; margin-bottom: 10px; }
.tag-badge {
  font-family: var(--mono); font-size: 10px; padding: 2px 9px;
  border-radius: 20px; background: var(--accent-bg);
  color: var(--accent); border: 1px solid var(--accent);
  white-space: nowrap; letter-spacing: 0.04em;
}
.para-preview { font-size: 12px; color: var(--text-secondary); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.para-num { margin-left: auto; font-family: var(--mono); font-size: 12px; color: var(--text-secondary); white-space: nowrap; }

.progress-bar { height: 2px; background: var(--border); border-radius: 1px; margin-bottom: 1.25rem; overflow: hidden; }
.progress-fill { height: 100%; background: var(--accent); border-radius: 1px; transition: width 0.4s; }

.btn-row { display: flex; gap: 8px; align-items: center; }
.btn {
  display: flex; align-items: center; gap: 6px;
  border: 1px solid var(--border-light); border-radius: var(--r-md);
  padding: 8px 16px; font-size: 13px; font-weight: 500;
  color: var(--text); transition: all 0.15s;
}
.btn:hover { background: var(--bg-card-hover); border-color: var(--border-focus); }
.btn.primary { background: var(--accent); color: #fff; border-color: var(--accent); }
.btn.primary:hover { background: var(--accent-light); border-color: var(--accent-light); }
.btn-icon { font-size: 14px; }
.stats { margin-left: auto; display: flex; gap: 24px; }
.stat { text-align: right; }
.stat-val { font-family: var(--mono); font-size: 18px; font-weight: 500; color: var(--text); }
.stat-val small { font-size: 12px; color: var(--text-secondary); }
.stat-lbl { font-size: 11px; color: var(--text-secondary); margin-top: 1px; }
</style>
