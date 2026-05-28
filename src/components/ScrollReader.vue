<template>
  <div>
    <div class="reader-area">
      <div class="mask top"></div>
      <div class="mask bottom"></div>
      <div class="focus-line"></div>
      <div ref="trackRef" class="track" :style="{ transform: `translateY(${Math.round(scrollY)}px)` }">
        <div v-for="(para, i) in paragraphs" :key="i" class="para-block">
          <div class="para-tag">{{ para.tag }}</div>
          <p class="para-text" :style="{ fontSize: fontSize + 'px' }">{{ para.text }}</p>
        </div>
      </div>
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
          <div class="stat-val">{{ paragraphs.length }}</div>
          <div class="stat-lbl">paragraf</div>
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

const scrollY = ref(80)
const isPlaying = ref(false)
const currentIdx = ref(0)
const trackRef = ref(null)

const progress = computed(() => {
  if (!props.paragraphs.length) return 0
  return Math.min(100, Math.round((currentIdx.value / props.paragraphs.length) * 100))
})

function buildBlockSpeeds() {
  if (!trackRef.value) return []
  const blocks = trackRef.value.querySelectorAll('.para-block')
  return Array.from(blocks).map((block, i) => {
    const wordCount = props.paragraphs[i]?.text.split(' ').length ?? 50
    const durationSec = wordCount / (props.speed / 60)
    return block.offsetHeight / durationSec
  })
}

let blockSpeeds = []
let animFrame = null
let lastTime = null

function currentPxPerSec() {
  return blockSpeeds[currentIdx.value] ?? blockSpeeds[0] ?? 60
}

function animate(ts) {
  if (!lastTime) lastTime = ts
  const dt = (ts - lastTime) / 1000
  lastTime = ts
  scrollY.value -= currentPxPerSec() * dt

  if (trackRef.value) {
    const trackH = trackRef.value.scrollHeight
    const blocks = trackRef.value.querySelectorAll('.para-block')
    const viewCenter = 100
    blocks.forEach((b, i) => {
      const bMid = b.offsetTop + scrollY.value + b.offsetHeight / 2
      if (Math.abs(bMid - viewCenter) < b.offsetHeight / 2) currentIdx.value = i
    })
    if (-scrollY.value > trackH - 180) { stop(); currentIdx.value = props.paragraphs.length; return }
  }
  animFrame = requestAnimationFrame(animate)
}

function start() {
  blockSpeeds = buildBlockSpeeds()
  isPlaying.value = true
  lastTime = null
  animFrame = requestAnimationFrame(animate)
}
function stop() {
  isPlaying.value = false
  if (animFrame) cancelAnimationFrame(animFrame)
  animFrame = null
}
function togglePlay() { isPlaying.value ? stop() : start() }
function reset() { stop(); scrollY.value = 80; currentIdx.value = 0; blockSpeeds = [] }

watch(() => props.paragraphs, reset)
watch(() => [props.speed, props.fontSize], () => { if (blockSpeeds.length) blockSpeeds = buildBlockSpeeds() })
onUnmounted(stop)
</script>

<style scoped>
.reader-area {
  position: relative;
  height: 230px;
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  background: var(--bg);
  overflow: hidden;
  margin-bottom: 10px;
}
.mask {
  position: absolute; left: 0; right: 0; height: 75px; z-index: 2; pointer-events: none;
}
.mask.top    { top: 0;    background: linear-gradient(to bottom, var(--bg), transparent); }
.mask.bottom { bottom: 0; background: linear-gradient(to top,   var(--bg), transparent); }
.focus-line {
  position: absolute; top: 50%; left: 0; right: 0;
  height: 1px; background: var(--accent); opacity: 0.4;
  z-index: 3; transform: translateY(-50%);
}
.track { position: absolute; top: 0; left: 0; right: 0; padding: 0 1.75rem; }
.para-block {
  padding: 1.1rem 0;
  border-bottom: 1px solid var(--border);
}
.para-block:last-child { border-bottom: none; }
.para-tag {
  font-family: var(--mono);
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--accent);
  margin-bottom: 7px;
  opacity: 0.8;
}
.para-text { line-height: 1.85; color: var(--text); }

.progress-bar {
  height: 2px; background: var(--border); border-radius: 1px;
  margin-bottom: 1.25rem; overflow: hidden;
}
.progress-fill {
  height: 100%; background: var(--accent); border-radius: 1px; transition: width 0.4s;
}
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
