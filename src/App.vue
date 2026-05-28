<template>
  <div class="app">

    <!-- Header — Node.js tarzı ince üst bar -->
    <header class="header">
      <div class="header-inner">
        <div class="logo">
          <span class="logo-hex">⬡</span>
          <span class="logo-text">hızlı<strong>okuma</strong></span>
        </div>
        <nav class="nav">
          <span class="nav-tag">Vue 3 + Vite</span>
        </nav>
      </div>
    </header>

    <main class="main">

      <!-- Hero başlık -->
      <section class="hero">
        <h1 class="hero-title">Okuma hızını geliştir.</h1>
        <p class="hero-sub">
          İki farklı egzersiz moduyla kelime/dakika hızını artır,
          odaklanma ve anlama becerini güçlendir.
        </p>
      </section>

      <!-- Mod seçici — Node.js download butonları gibi -->
      <div class="mode-tabs">
        <button
          :class="['tab', mode === 'scroll' ? 'active' : '']"
          @click="mode = 'scroll'"
        >
          <span class="tab-icon">↕</span>
          <span>
            <span class="tab-title">Paragraf Modu</span>
            <span class="tab-desc">Anlama ve bağlam</span>
          </span>
        </button>
        <button
          :class="['tab', mode === 'ticker' ? 'active' : '']"
          @click="mode = 'ticker'"
        >
          <span class="tab-icon">←</span>
          <span>
            <span class="tab-title">Ticker Modu ( Alpha )</span>
            <span class="tab-desc">Göz refleksi ve dikkat</span>
          </span>
        </button>
      </div>

      <!-- Kategori filtresi -->
      <CategoryPills v-model="activeCategory" :categories="CATEGORIES" />

      <!-- Kontroller -->
      <ControlPanel v-model:speed="speed" v-model:fontSize="fontSize" />

      <!-- Okuyucu -->
      <div class="reader-card">
        <ScrollReader
          v-if="mode === 'scroll'"
          :paragraphs="filteredParagraphs"
          :speed="speed"
          :fontSize="fontSize"
        />
        <TickerReader
          v-else
          :paragraphs="filteredParagraphs"
          :speed="speed"
          :fontSize="fontSize"
        />
      </div>

    </main>

    <footer class="footer">
      <span>Hızlı Okuma — Vue 3 + Vite Demo</span>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { PARAGRAPHS, CATEGORIES } from './data/paragraphs.js'
import CategoryPills from './components/CategoryPills.vue'
import ControlPanel from './components/ControlPanel.vue'
import ScrollReader from './components/ScrollReader.vue'
import TickerReader from './components/TickerReader.vue'

const mode = ref('scroll')
const activeCategory = ref('Tümü')
const speed = ref(60)
const fontSize = ref(17)

const filteredParagraphs = computed(() => {
  if (activeCategory.value === 'Tümü') return PARAGRAPHS
  return PARAGRAPHS.filter(p => p.tag === activeCategory.value)
})
</script>

<style scoped>
.app { min-height: 100vh; display: flex; flex-direction: column; }

/* ── Header ── */
.header {
  border-bottom: 1px solid var(--border);
  background: rgba(15,15,15,0.85);
  backdrop-filter: blur(12px);
  position: sticky;
  top: 0;
  z-index: 10;
}
.header-inner {
  max-width: 760px;
  margin: 0 auto;
  padding: 0 1.5rem;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.logo {
  display: flex;
  align-items: center;
  gap: 9px;
}
.logo-hex {
  font-size: 22px;
  color: var(--accent);
  line-height: 1;
}
.logo-text {
  font-size: 16px;
  font-weight: 300;
  color: var(--text);
  letter-spacing: -0.02em;
}
.logo-text strong { font-weight: 600; color: var(--accent); }
.nav-tag {
  font-family: var(--mono);
  font-size: 12px;
  color: var(--text-secondary);
  background: var(--bg-card);
  border: 1px solid var(--border-light);
  padding: 3px 10px;
  border-radius: 20px;
}

/* ── Main ── */
.main {
  max-width: 760px;
  margin: 0 auto;
  padding: 3rem 1.5rem 2rem;
  flex: 1;
  width: 100%;
}

/* ── Hero ── */
.hero { margin-bottom: 2.5rem; }
.hero-title {
  font-size: 32px;
  font-weight: 600;
  letter-spacing: -0.04em;
  color: var(--text);
  margin-bottom: 10px;
  line-height: 1.2;
}
.hero-sub {
  font-size: 15px;
  color: var(--text-secondary);
  line-height: 1.7;
  max-width: 520px;
}

/* ── Mod seçici ── */
.mode-tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 1.5rem;
}
.tab {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 20px;
  border: 1px solid var(--border-light);
  border-radius: var(--r-lg);
  background: var(--bg-card);
  color: var(--text-secondary);
  transition: all 0.15s;
  text-align: left;
  flex: 1;
}
.tab:hover { border-color: var(--border-focus); background: var(--bg-card-hover); }
.tab.active {
  border-color: var(--accent);
  background: var(--accent-bg);
  color: var(--text);
}
.tab-icon {
  font-size: 18px;
  color: var(--accent);
  flex-shrink: 0;
  width: 28px;
  text-align: center;
}
.tab-title {
  display: block;
  font-size: 14px;
  font-weight: 500;
  color: var(--text);
}
.tab-desc {
  display: block;
  font-size: 12px;
  color: var(--text-secondary);
  margin-top: 1px;
}

/* ── Reader card ── */
.reader-card {
  background: var(--bg-card);
  border: 1px solid var(--border);
  border-radius: var(--r-xl);
  padding: 1.5rem;
}

/* ── Footer ── */
.footer {
  border-top: 1px solid var(--border);
  padding: 1.25rem 1.5rem;
  text-align: center;
  font-size: 12px;
  color: var(--text-secondary);
  font-family: var(--mono);
}
</style>
