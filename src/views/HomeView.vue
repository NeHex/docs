<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

type StackItem = {
  name: string
  desc: string
  iconSrc?: string
  badge?: string
  invert?: boolean
}

type DeploymentOption = {
  id: string
  title: string
  subtitle: string
  commandLines: string[]
}

const heroRef = ref<HTMLElement | null>(null)
const featuresRef = ref<HTMLElement | null>(null)
const architectureRef = ref<HTMLElement | null>(null)
const deploymentRef = ref<HTMLElement | null>(null)

const heroScrollProgress = ref(0)
const featuresRevealProgress = ref(0)
const architectureRevealProgress = ref(0)
const deploymentRevealProgress = ref(0)

const clamp = (value: number, min = 0, max = 1) => Math.min(max, Math.max(min, value))

const toExitTitleProgress = (sectionProgress: number) => clamp(sectionProgress / 0.5)
const toExitRestProgress = (sectionProgress: number) => clamp((sectionProgress - 0.34) / 0.66)
const toEnterTitleProgress = (sectionProgress: number) => clamp(sectionProgress / 0.62)
const toEnterRestProgress = (sectionProgress: number) => clamp((sectionProgress - 0.28) / 0.72)

const createExitTitleStyle = (progress: number) => {
  const p = toExitTitleProgress(progress)
  return {
    opacity: String(1 - p),
    transform: `scale(${1 - p * 0.2})`,
  }
}

const createExitRestStyle = (progress: number) => {
  const p = toExitRestProgress(progress)
  return {
    opacity: String(1 - p),
    transform: `scale(${1 - p * 0.14})`,
  }
}

const createEnterTitleStyle = (progress: number) => {
  const p = toEnterTitleProgress(progress)
  return {
    opacity: String(p),
    transform: `scale(${0.8 + p * 0.2})`,
  }
}

const createEnterRestStyle = (progress: number) => {
  const p = toEnterRestProgress(progress)
  return {
    opacity: String(p),
    transform: `scale(${0.86 + p * 0.14})`,
  }
}

const heroTitleStyle = computed(() => createExitTitleStyle(heroScrollProgress.value))
const heroRestStyle = computed(() => createExitRestStyle(heroScrollProgress.value))
const featuresTitleStyle = computed(() => createEnterTitleStyle(featuresRevealProgress.value))
const featuresRestStyle = computed(() => createEnterRestStyle(featuresRevealProgress.value))
const architectureTitleStyle = computed(() => createEnterTitleStyle(architectureRevealProgress.value))
const architectureRestStyle = computed(() => createEnterRestStyle(architectureRevealProgress.value))
const deploymentTitleStyle = computed(() => createEnterTitleStyle(deploymentRevealProgress.value))
const deploymentRestStyle = computed(() => createEnterRestStyle(deploymentRevealProgress.value))

const getSectionScrollProgress = (sectionEl: HTMLElement | null) => {
  if (!sectionEl) return 0

  const rect = sectionEl.getBoundingClientRect()
  const sectionHeight = sectionEl.offsetHeight || 1
  const scrolled = Math.max(0, -rect.top)
  const duration = sectionHeight * 0.82

  return clamp(scrolled / duration)
}

const getSectionRevealProgress = (sectionEl: HTMLElement | null) => {
  if (!sectionEl) return 0

  const rect = sectionEl.getBoundingClientRect()
  const viewportHeight = window.innerHeight || 1
  const revealStart = viewportHeight * 0.88
  const revealEnd = viewportHeight * 0.24

  return clamp((revealStart - rect.top) / (revealStart - revealEnd))
}

const updateSectionScrollProgress = () => {
  heroScrollProgress.value = getSectionScrollProgress(heroRef.value)
  featuresRevealProgress.value = getSectionRevealProgress(featuresRef.value)
  architectureRevealProgress.value = getSectionRevealProgress(architectureRef.value)
  deploymentRevealProgress.value = getSectionRevealProgress(deploymentRef.value)
}

let ticking = false

const handleViewportChange = () => {
  if (ticking) return
  ticking = true
  requestAnimationFrame(() => {
    updateSectionScrollProgress()
    ticking = false
  })
}

onMounted(() => {
  updateSectionScrollProgress()
  window.addEventListener('scroll', handleViewportChange, { passive: true })
  window.addEventListener('resize', handleViewportChange)
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleViewportChange)
  window.removeEventListener('resize', handleViewportChange)
})

const featureCards = [
  {
    iconSrc: '/images/nuxt-svgrepo-com.svg',
    iconAlt: 'Nuxt 图标',
    title: 'Nuxt前端',
    description: '更好的SEO优化，更流畅的阅读体验，更好的开发支持。',
    tone: 'cyan',
  },
  {
    iconSrc: '/images/rust-svgrepo-com.svg',
    iconAlt: 'Rust 图标',
    title: 'Rust 后端',
    description: '使用Rust开发的API，异步零开销，I/O 无性能损耗；单机扛高并发。',
    tone: 'violet',
  },
  {
    iconSrc: '/images/setting-5-svgrepo-com.svg',
    iconAlt: '设置图标',
    title: '极高的自定义',
    description: '你可以自定义每一个页面的任何元素，甚至因为开源，你还可以自行更改后端。',
    tone: 'green',
  },
]

const metrics = [
  {
    iconSrc: '/images/lightning-bolt-svgrepo-com.svg',
    iconAlt: '',
    value: '<83ms',
    label: '极速响应',
  },
  {
    iconSrc: '/images/shield-alt-svgrepo-com.svg',
    iconAlt: '',
    value: '99.9%',
    label: '安全可靠',
  },
  {
    iconSrc: '/images/database-02-svgrepo-com.svg',
    iconAlt: '',
    value: '50+',
    label: '灵活扩展',
  },
  {
    iconSrc: '/images/github-142-svgrepo-com.svg',
    iconAlt: '',
    value: '1.2.+',
    label: '每周更新',
  },
]

const frontendStack: StackItem[] = [
  {
    name: 'Nuxt 3',
    desc: 'SSR / SSG',
    iconSrc: '/images/nuxt-svgrepo-com.svg',
  },
  {
    name: 'TypeScript',
    desc: '类型安全',
    badge: 'TS',
  },
  {
    name: 'Vue 3',
    desc: '组合式 API',
    badge: 'V3',
  },
  {
    name: 'UI System',
    desc: '组件化设计',
    iconSrc: '/images/setting-5-svgrepo-com.svg',
    invert: true,
  },
]

const backendStack: StackItem[] = [
  {
    name: 'Rust',
    desc: '高性能核心',
    iconSrc: '/images/rust-svgrepo-com.svg',
    invert: true,
  },
  {
    name: 'Actix / Axum',
    desc: '异步服务',
    iconSrc: '/images/fast-svgrepo-com.svg',
    invert: true,
  },
  {
    name: 'PostgreSQL',
    desc: '持久化存储',
    iconSrc: '/images/database-02-svgrepo-com.svg',
    invert: true,
  },
  {
    name: 'GitHub Actions',
    desc: '自动化发布',
    iconSrc: '/images/github-142-svgrepo-com.svg',
    invert: true,
  },
]

const deploymentOptions: DeploymentOption[] = [
  {
    id: 'compose',
    title: 'Docker Compose',
    subtitle: 'dockse compose up -d',
    commandLines: [
      'None.',
    ],
  },
  {
    id: 'docker',
    title: 'Docker',
    subtitle: 'docker run -d',
    commandLines: [
      'None'
    ],
  },
  {
    id: 'binary',
    title: '二进制文件运行',
    subtitle: './nehex-linux-amd64',
    commandLines: [
      'None.'
    ],
  },
  {
    id: 'source',
    title: '从源文件部署',
    subtitle: './nehex',
    commandLines: [
      'None.'
    ],
  },
]

const deploymentFallback: DeploymentOption = {
  id: 'compose',
  title: 'Docker Compose',
  subtitle: '一键启动完整服务',
  commandLines: ['docker compose up -d'],
}

const activeDeploymentId = ref(deploymentOptions[0]?.id ?? deploymentFallback.id)
const activeDeployment = computed<DeploymentOption>(
  () =>
    deploymentOptions.find((item) => item.id === activeDeploymentId.value) ??
    deploymentOptions[0] ??
    deploymentFallback,
)
</script>

<template>
  <section ref="heroRef" class="hero">
    <div class="hero-inner">
      <p class="hero-kicker hero-rest-animate" :style="heroRestStyle">OPENSOURCE PERSONAL SPACE ENGINE</p>
      <h1 class="hero-title" :style="heroTitleStyle">
        <span class="hero-title-primary">NeHex</span>
        <span class="hero-title-secondary">Blog</span>
      </h1>
      <p class="hero-description hero-rest-animate" :style="heroRestStyle">
        NeHex 是一个前后端分离的次世代开源个人博客引擎,采用Rust构建的引擎可以快速反应，具有更高的稳定性与拓展性；
      </p>
      <div class="hero-actions hero-rest-animate" :style="heroRestStyle">
        <a class="hero-btn hero-btn-primary" href="#quick-start">开始使用</a>
      </div>
    </div>
  </section>

  <section id="features" ref="featuresRef" class="features">
    <div class="features-inner">
      <p class="features-kicker section-rest-animate" :style="featuresRestStyle">FEATURES</p>
      <h2 class="features-title section-title-animate" :style="featuresTitleStyle">
        把权限
        <span>还给用户</span>
      </h2>
      <p class="features-subtitle section-rest-animate" :style="featuresRestStyle">
        以开源做基底，把安全和自由还给用户
      </p>

      <div class="feature-grid section-rest-animate" :style="featuresRestStyle">
        <article v-for="card in featureCards" :key="card.title" class="feature-card">
          <div class="feature-icon" :class="`feature-icon-${card.tone}`">
            <img class="feature-icon-image" :src="card.iconSrc" :alt="card.iconAlt" />
          </div>
          <h3 class="feature-card-title">{{ card.title }}</h3>
          <p class="feature-card-desc">{{ card.description }}</p>
        </article>
      </div>

      <div class="metrics-grid section-rest-animate" :style="featuresRestStyle">
        <article v-for="item in metrics" :key="item.value" class="metric-card">
          <div class="metric-icon">
            <img class="metric-icon-image" :src="item.iconSrc" :alt="item.iconAlt" />
          </div>
          <p class="metric-value">{{ item.value }}</p>
          <p class="metric-label">{{ item.label }}</p>
        </article>
      </div>
    </div>
  </section>

  <section id="architecture" ref="architectureRef" class="architecture">
    <div class="architecture-inner">
      <p class="architecture-kicker section-rest-animate" :style="architectureRestStyle">ARCHITECTURE</p>
      <h2 class="architecture-title section-title-animate" :style="architectureTitleStyle">技术架构</h2>
      <p class="architecture-subtitle section-rest-animate" :style="architectureRestStyle">
        前后端分层解耦，以数据流驱动页面与服务协同工作
      </p>

      <div class="architecture-layout section-rest-animate" :style="architectureRestStyle">
        <article class="arch-card">
          <header class="arch-card-header">
            <span class="arch-pill">Frontend</span>
            <h3 class="arch-card-title">前端层</h3>
          </header>

          <div class="stack-grid">
            <article v-for="item in frontendStack" :key="item.name" class="stack-item">
              <div class="stack-icon">
                <img
                  v-if="item.iconSrc"
                  class="stack-icon-image"
                  :class="{ 'stack-icon-image-invert': item.invert }"
                  :src="item.iconSrc"
                  :alt="item.name"
                />
                <span v-else class="stack-icon-badge">{{ item.badge }}</span>
              </div>
              <div class="stack-text">
                <p class="stack-name">{{ item.name }}</p>
                <p class="stack-desc">{{ item.desc }}</p>
              </div>
            </article>
          </div>
        </article>

        <div class="data-flow" aria-hidden="true">
          <span class="data-flow-label">DATA FLOW</span>
          <div class="data-flow-line">
            <span class="data-flow-dot"></span>
            <span class="data-flow-dot"></span>
            <span class="data-flow-dot"></span>
          </div>
        </div>

        <article class="arch-card">
          <header class="arch-card-header">
            <span class="arch-pill">Backend</span>
            <h3 class="arch-card-title">后端层</h3>
          </header>

          <div class="stack-grid">
            <article v-for="item in backendStack" :key="item.name" class="stack-item">
              <div class="stack-icon">
                <img
                  v-if="item.iconSrc"
                  class="stack-icon-image"
                  :class="{ 'stack-icon-image-invert': item.invert }"
                  :src="item.iconSrc"
                  :alt="item.name"
                />
                <span v-else class="stack-icon-badge">{{ item.badge }}</span>
              </div>
              <div class="stack-text">
                <p class="stack-name">{{ item.name }}</p>
                <p class="stack-desc">{{ item.desc }}</p>
              </div>
            </article>
          </div>
        </article>
      </div>
    </div>
  </section>

  <section id="deployment" ref="deploymentRef" class="deployment">
    <div class="deployment-inner">
      <p class="deployment-kicker section-rest-animate" :style="deploymentRestStyle">DEPLOYMENT</p>
      <h2 class="deployment-title section-title-animate" :style="deploymentTitleStyle">只需几分钟，即可使用</h2>

      <div class="deployment-layout section-rest-animate" :style="deploymentRestStyle">
        <div class="deploy-options">
          <button
            v-for="item in deploymentOptions"
            :key="item.id"
            class="deploy-option-card"
            :class="{ 'deploy-option-card-active': item.id === activeDeploymentId }"
            type="button"
            @click="activeDeploymentId = item.id"
          >
            <p class="deploy-option-title">{{ item.title }}</p>
            <p class="deploy-option-subtitle">{{ item.subtitle }}</p>
          </button>
        </div>

        <Transition name="terminal-swap" mode="out-in">
          <article :key="activeDeployment.id" class="deploy-terminal">
            <header class="deploy-terminal-header">
              <div class="deploy-terminal-dots">
                <span></span>
                <span></span>
                <span></span>
              </div>
              <p class="deploy-terminal-title">{{ activeDeployment.title }}</p>
            </header>

            <pre class="deploy-terminal-body"><code><span v-for="line in activeDeployment.commandLines" :key="line">$ {{ line }}</span></code></pre>
          </article>
        </Transition>
      </div>
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;
  min-height: calc(100svh - 88px);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: 24px 0;
}

.hero-inner {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 36px 24px;
  text-align: center;
}

.hero-inner::before {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(
    circle at 50% 50%,
    rgba(0, 102, 255, 0.22) 0%,
    rgba(0, 28, 92, 0.18) 28%,
    rgba(0, 0, 0, 0) 42%
  );
  pointer-events: none;
  z-index: 0;
  transform-origin: center;
  animation: hero-glow-pulse 10s ease-in-out infinite;
}

.hero-inner > * {
  position: relative;
  z-index: 1;
}

@keyframes hero-glow-pulse {
  0% {
    transform: scale(1);
  }

  50% {
    transform: scale(1.35);
  }

  100% {
    transform: scale(1);
  }
}

.hero-kicker {
  margin-bottom: 15px;
  color: #3a8fff;
  letter-spacing: 0.18em;
  font-size: 13px;
  font-weight: 500;
  text-transform: uppercase;
}

.hero-title {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0;
  margin: 0;
  line-height: 0.93;
  letter-spacing: -0.02em;
  transform-origin: 50% 50%;
  will-change: transform, opacity;
}

.hero-title-primary {
  font-size: clamp(36px, 7vw, 114px);
  font-weight: 800;
  background: linear-gradient(90deg, #90aaff 0%, #6186ff 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.hero-title-secondary {
  font-size: clamp(34px, 6.5vw, 103px);
  font-weight: 800;
  color: #e8edf4;
}

.hero-description {
  max-width: 820px;
  margin: 1.5em auto 0;
  color: rgba(234, 239, 247, 0.72);
  font-size: clamp(14px, 1.1vw, 23px);
  line-height: 1.58;
  font-weight: 400;
}

.hero-actions {
  margin-top: 22px;
  display: flex;
  justify-content: center;
  gap: 10px;
  flex-wrap: wrap;
}

.hero-rest-animate {
  transform-origin: 50% 50%;
  will-change: transform, opacity;
}

.section-title-animate {
  transform-origin: 50% 50%;
  will-change: transform, opacity;
}

.section-rest-animate {
  transform-origin: 50% 50%;
  will-change: transform, opacity;
}

.hero-btn {
  min-width: 110px;
  padding: 8px 16px;
  border-radius: 999px;
  text-decoration: none;
  font-size: 15px;
  font-weight: 600;
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease,
    border-color 0.2s ease,
    color 0.2s ease;
}

.hero-btn:hover {
  transform: translateY(-1px);
}

.hero-btn-primary {
  color: #1c1c1c;
  background: #ffffff;
  border: 1px solid #ffffff;
  box-shadow: 0 8px 24px rgba(255, 255, 255, 0.15);
}

.hero-btn-secondary {
  color: #f4f8ff;
  border: 1px solid rgba(196, 218, 247, 0.28);
  background: rgba(20, 20, 20, 0.5);
}

.hero-btn-secondary:hover {
  color: #9bc6ff;
  border-color: rgba(155, 198, 255, 0.5);
}

.features {
  position: relative;
  min-height: calc(100svh - 88px);
  display: flex;
  align-items: center;
  padding: 24px 0;
}

.features::before {
  content: '';
  position: absolute;
  inset: 6% 16%;
  background: radial-gradient(circle at center, rgba(0, 86, 255, 0.2), rgba(0, 0, 0, 0) 46%);
  pointer-events: none;
}

.features-inner {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 36px 24px;
  text-align: center;
}

.features-kicker {
  color: #3b86ff;
  font-size: 13px;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.features-title {
  margin-top: 6px;
  color: #edf2f7;
  font-size: clamp(36px, 7vw, 114px);
  line-height: 1.2;
  font-weight: 700;
}

.features-title span {
  color: #00cfff;
}

.features-subtitle {
  margin-top: 14px;
  color: rgba(234, 239, 247, 0.55);
  font-size: clamp(12px, 1.2vw, 18px);
}

.feature-grid {
  margin-top: 38px;
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 16px;
}

.feature-card {
  text-align: left;
  border-radius: 16px;
  border: 1px solid rgba(182, 210, 245, 0.1);
  background: rgba(8, 10, 16, 0.62);
  backdrop-filter: blur(6px);
  padding: 30px;
}

.feature-icon {
  width: 70px;
  height: 70px;
  border-radius: 20px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.feature-icon-image {
  width: 55px;
  height: 55px;
  object-fit: contain;
}

.feature-icon-cyan {
  background: #3dfde0;
}

.feature-icon-violet {
  background: white;
}

.feature-icon-green {
  background: #8EA8FF;
}

.feature-card-title {
  margin-top: 12px;
  color: #f0f5ff;
  font-size: 19px;
  font-weight: 600;
}

.feature-card-desc {
  margin-top: 8px;
  color: rgba(230, 236, 245, 0.45);
  font-size: 15px;
  line-height: 1.6;
}

.metrics-grid {
  margin-top: 24px;
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 10px;
}

.metric-card {
  border-radius: 12px;
  background: rgba(0, 0, 0, 0.55);
  padding: 30px 8px;
}

.metric-icon {
  width: 20px;
  height: 20px;
  margin: 0 auto;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.metric-icon-image {
  width: 30px;
  height: 30px;
  object-fit: contain;
  filter: brightness(0) invert(0.88);
  opacity: 0.85;
}

.metric-value {
  margin-top: 8px;
  color: #f4f7fb;
  font-size: 32px;
  font-weight: 700;
  line-height: 1.1;
}

.metric-label {
  margin-top: 4px;
  color: rgba(227, 234, 244, 0.45);
  font-size: 13px;
}

.architecture {
  position: relative;
  min-height: calc(100svh - 88px);
  display: flex;
  align-items: center;
  padding: 24px 0;
}

.architecture::before {
  content: '';
  position: absolute;
  inset: 10% 18%;
  background: radial-gradient(circle at center, rgba(0, 100, 255, 0.16), rgba(0, 0, 0, 0) 48%);
  pointer-events: none;
}

.architecture-inner {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 36px 24px;
  text-align: center;
}

.architecture-kicker {
  color: #3b86ff;
  font-size: 13px;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.architecture-title {
  margin-top: 6px;
  color: #edf2f7;
  font-size: clamp(30px, 5vw, 56px);
  line-height: 1.15;
  font-weight: 700;
}

.architecture-subtitle {
  margin-top: 12px;
  color: rgba(234, 239, 247, 0.55);
  font-size: clamp(12px, 1.2vw, 18px);
}

.architecture-layout {
  margin-top: 34px;
  display: grid;
  grid-template-columns: minmax(0, 1fr) 140px minmax(0, 1fr);
  align-items: center;
  gap: 14px;
}

.arch-card {
  border-radius: 18px;
  border: 1px solid rgba(178, 205, 241, 0.14);
  background: rgba(8, 10, 16, 0.66);
  backdrop-filter: blur(8px);
  padding: 20px;
  text-align: left;
}

.arch-card-header {
  margin-bottom: 14px;
}

.arch-pill {
  display: inline-flex;
  align-items: center;
  height: 22px;
  padding: 0 10px;
  border-radius: 999px;
  border: 1px solid rgba(132, 173, 223, 0.4);
  color: #a4ccff;
  font-size: 11px;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.arch-card-title {
  margin-top: 10px;
  color: #f2f6fd;
  font-size: 24px;
  font-weight: 700;
}

.stack-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
}

.stack-item {
  display: flex;
  align-items: center;
  gap: 10px;
  border-radius: 12px;
  background: rgba(0, 0, 0, 0.4);
  border: 1px solid rgba(180, 205, 236, 0.08);
  padding: 10px;
}

.stack-icon {
  width: 38px;
  height: 38px;
  border-radius: 10px;
  background: rgba(30, 53, 80, 0.45);
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.stack-icon-image {
  width: 24px;
  height: 24px;
  object-fit: contain;
}

.stack-icon-image-invert {
  filter: brightness(0) invert(0.9);
  opacity: 0.88;
}

.stack-icon-badge {
  color: #d9e8ff;
  font-size: 13px;
  font-weight: 700;
  letter-spacing: 0.03em;
}

.stack-text {
  min-width: 0;
}

.stack-name {
  color: #f6f9ff;
  font-size: 15px;
  font-weight: 600;
  line-height: 1.25;
}

.stack-desc {
  margin-top: 2px;
  color: rgba(220, 231, 247, 0.52);
  font-size: 12px;
  line-height: 1.3;
}

.data-flow {
  position: relative;
  align-self: stretch;
  display: flex;
  align-items: center;
  justify-content: center;
}

.data-flow-label {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, calc(-50% - 18px));
  color: rgba(149, 191, 241, 0.72);
  font-size: 10px;
  letter-spacing: 0.14em;
  white-space: nowrap;
}

.data-flow-line {
  position: relative;
  width: 100%;
  height: 2px;
  border-radius: 999px;
  background: linear-gradient(
    90deg,
    rgba(95, 154, 241, 0.08) 0%,
    rgba(95, 154, 241, 0.95) 50%,
    rgba(95, 154, 241, 0.08) 100%
  );
  box-shadow: 0 0 20px rgba(80, 147, 242, 0.35);
}

.data-flow-dot {
  position: absolute;
  top: 50%;
  left: 0;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #8fd1ff;
  box-shadow: 0 0 10px rgba(105, 183, 255, 0.9);
  transform: translate(-50%, -50%);
  animation: data-flow-forward 2.8s linear infinite;
}

.data-flow-dot:nth-child(2) {
  animation-delay: 0.85s;
}

.data-flow-dot:nth-child(3) {
  animation-delay: 1.7s;
}

@keyframes data-flow-forward {
  0% {
    left: 0%;
    opacity: 0;
  }

  18% {
    opacity: 1;
  }

  100% {
    left: 100%;
    opacity: 0;
  }
}

.deployment {
  position: relative;
  min-height: calc(100svh - 88px);
  display: flex;
  align-items: center;
  padding: 24px 0;
}

.deployment::before {
  content: '';
  position: absolute;
  inset: 12% 18%;
  background: radial-gradient(circle at center, rgba(0, 98, 245, 0.15), rgba(0, 0, 0, 0) 50%);
  pointer-events: none;
}

.deployment-inner {
  position: relative;
  z-index: 1;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 36px 24px;
  text-align: center;
}

.deployment-kicker {
  color: #3b86ff;
  font-size: 13px;
  letter-spacing: 0.2em;
  text-transform: uppercase;
}

.deployment-title {
  margin-top: 8px;
  color: #edf2f7;
  font-size: clamp(30px, 4.8vw, 52px);
  line-height: 1.15;
  font-weight: 700;
}

.deployment-layout {
  margin-top: 28px;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 16px;
  align-items: stretch;
  min-height: 0;
}

.deploy-options {
  display: grid;
  gap: 12px;
  grid-auto-rows: 1fr;
}

.deploy-option-card {
  width: 100%;
  border: 1px solid rgba(170, 197, 232, 0.16);
  border-radius: 14px;
  background: rgba(6, 9, 16, 0.66);
  padding: 16px;
  min-height: 94px;
  text-align: left;
  cursor: pointer;
  transition:
    border-color 0.2s ease,
    transform 0.2s ease,
    background-color 0.2s ease,
    box-shadow 0.2s ease;
}

.deploy-option-card:hover {
  transform: translateY(-1px);
  border-color: rgba(126, 180, 245, 0.38);
}

.deploy-option-card-active {
  border-color: rgba(126, 180, 245, 0.72);
  background: rgba(10, 17, 30, 0.9);
  box-shadow: 0 8px 22px rgba(58, 128, 233, 0.25);
}

.deploy-option-title {
  color: #f0f6ff;
  font-size: 19px;
  font-weight: 650;
  line-height: 1.2;
}

.deploy-option-subtitle {
  margin-top: 7px;
  color: rgba(218, 230, 246, 0.55);
  font-size: 14px;
  line-height: 1.4;
}

.deploy-terminal {
  border-radius: 16px;
  border: 1px solid rgba(179, 206, 241, 0.18);
  background: rgba(5, 8, 13, 0.9);
  overflow: hidden;
  text-align: left;
  box-shadow: 0 14px 36px rgba(0, 0, 0, 0.35);
}

.deploy-terminal-header {
  height: 44px;
  padding: 0 14px;
  display: flex;
  align-items: center;
  gap: 10px;
  border-bottom: 1px solid rgba(178, 204, 240, 0.14);
  background: rgba(18, 23, 34, 0.92);
}

.deploy-terminal-dots {
  display: inline-flex;
  gap: 6px;
  flex-shrink: 0;
}

.deploy-terminal-dots span {
  width: 9px;
  height: 9px;
  border-radius: 50%;
  background: #617899;
}

.deploy-terminal-dots span:nth-child(1) {
  background: #ff5f56;
}

.deploy-terminal-dots span:nth-child(2) {
  background: #ffbd2e;
}

.deploy-terminal-dots span:nth-child(3) {
  background: #27c93f;
}

.deploy-terminal-title {
  color: rgba(230, 239, 250, 0.78);
  font-size: 13px;
  line-height: 1;
}

.deploy-terminal-body {
  margin: 0;
  padding: 16px 18px 20px;
  min-height: 330px;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New', monospace;
  font-size: 13px;
  line-height: 1.8;
  color: #bce1ff;
  white-space: pre-wrap;
  word-break: break-word;
}

.deploy-terminal-body code {
  display: block;
}

.deploy-terminal-body span {
  display: block;
}

.terminal-swap-enter-active,
.terminal-swap-leave-active {
  transition:
    opacity 0.26s ease,
    transform 0.26s ease,
    filter 0.26s ease;
}

.terminal-swap-enter-from {
  opacity: 0;
  transform: translateY(10px) scale(0.98);
  filter: blur(3px);
}

.terminal-swap-leave-to {
  opacity: 0;
  transform: translateY(-8px) scale(1.01);
  filter: blur(2px);
}

@media (max-width: 900px) {
  .hero {
    min-height: calc(100svh - 78px);
    padding: 18px 0;
  }

  .hero-inner {
    padding: 24px 10px;
  }

  .hero-kicker {
    margin-bottom: 8px;
    font-size: 10px;
    letter-spacing: 0.14em;
  }

  .hero-description {
    margin-top: 12px;
    font-size: 14px;
  }

  .hero-actions {
    margin-top: 16px;
    gap: 8px;
  }

  .hero-btn {
    min-width: 98px;
    padding: 7px 14px;
    font-size: 14px;
  }

  .features {
    min-height: calc(100svh - 78px);
    padding: 18px 0;
  }

  .features::before {
    inset: 12% 4%;
  }

  .features-inner {
    padding: 24px 10px;
  }

  .feature-grid {
    margin-top: 26px;
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .feature-card {
    padding: 14px;
  }

  .feature-card-title {
    font-size: 17px;
  }

  .feature-card-desc {
    font-size: 14px;
  }

  .metrics-grid {
    margin-top: 14px;
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .metric-value {
    font-size: 24px;
  }

  .architecture {
    min-height: calc(100svh - 78px);
    padding: 18px 0;
  }

  .architecture::before {
    inset: 10% 6%;
  }

  .architecture-inner {
    padding: 24px 10px;
  }

  .architecture-layout {
    margin-top: 24px;
    grid-template-columns: 1fr;
    gap: 12px;
  }

  .arch-card {
    padding: 14px;
  }

  .arch-card-title {
    font-size: 20px;
  }

  .stack-grid {
    grid-template-columns: 1fr;
  }

  .data-flow {
    height: 62px;
    align-self: auto;
  }

  .data-flow-label {
    transform: translate(-50%, -4px);
  }

  .data-flow-line {
    width: 2px;
    height: 100%;
    background: linear-gradient(
      180deg,
      rgba(95, 154, 241, 0.08) 0%,
      rgba(95, 154, 241, 0.95) 50%,
      rgba(95, 154, 241, 0.08) 100%
    );
  }

  .data-flow-dot {
    left: 50%;
    top: 0;
    transform: translate(-50%, -50%);
    animation-name: data-flow-vertical;
  }

  .deployment {
    min-height: calc(100svh - 78px);
    padding: 18px 0;
  }

  .deployment::before {
    inset: 12% 6%;
  }

  .deployment-inner {
    padding: 24px 10px;
  }

  .deployment-layout {
    margin-top: 20px;
    grid-template-columns: 1fr;
    gap: 12px;
    min-height: 0;
  }

  .deploy-option-card {
    padding: 12px 14px;
    min-height: 90px;
  }

  .deploy-option-title {
    font-size: 17px;
  }

  .deploy-option-subtitle {
    font-size: 13px;
  }

  .deploy-terminal-body {
    min-height: 230px;
    font-size: 12px;
    line-height: 1.65;
  }
}

@keyframes data-flow-vertical {
  0% {
    top: 0%;
    opacity: 0;
  }

  18% {
    opacity: 1;
  }

  100% {
    top: 100%;
    opacity: 0;
  }
}
</style>
