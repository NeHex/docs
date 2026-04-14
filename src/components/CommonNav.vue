<script setup lang="ts">
const navItems = [
  { label: '主题', href: '#theme' },
  { label: '文档', href: '#docs' },
  { label: 'GITHUB', href: 'https://github.com', external: true },
  { label: 'DEMO', href: '#demo' },
]
</script>

<template>
  <header class="common-nav">
    <div class="common-nav-inner">
      <div class="nav-left">
        <img class="nav-logo" src="/images/android-chrome-192x192.png" alt="NeHex Logo" />
        <span class="brand-name">NeHex</span>
      </div>

      <nav class="nav-center" aria-label="主导航">
        <a
          v-for="item in navItems"
          :key="item.label"
          class="nav-link"
          :href="item.href"
          :target="item.external ? '_blank' : undefined"
          :rel="item.external ? 'noopener noreferrer' : undefined"
        >
          {{ item.label }}
        </a>
      </nav>

      <div class="nav-right">
        <a class="quick-start-btn" href="#quick-start">快速开始</a>
      </div>
    </div>
  </header>
</template>

<style scoped>
.common-nav {
  position: sticky;
  top: 0;
  z-index: 1000;
  width: 100%;
  padding: 0 24px;
  background: #2c2c2c;
  border-bottom: 1px solid #3a3a3a;
}

.common-nav-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 28px;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 18px 0;
}

.nav-left {
  display: flex;
  align-items: center;
  gap: 10px;
  flex-shrink: 0;
}

.nav-logo {
  width: 38px;
  height: 38px;
  object-fit: contain;
}

.brand-name {
  font-size: 28px;
  font-weight: 800;
  line-height: 1;
  background: linear-gradient(90deg, #90aaff 0%, #6186ff 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.nav-center {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 24px;
  flex: 1;
}

.nav-link {
  color: #ececec;
  font-size: 15px;
  font-weight: 600;
  text-decoration: none;
  transition: color 0.2s ease;
}

.nav-link:hover {
  color: #8db8e7;
}

.nav-right {
  flex-shrink: 0;
}

.quick-start-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 10px 18px;
  color: #1d1d1d;
  font-size: 14px;
  font-weight: 700;
  text-decoration: none;
  border: 1px solid #ffffff;
  border-radius: 999px;
  background: #ffffff;
  transition:
    border-color 0.2s ease,
    color 0.2s ease,
    box-shadow 0.2s ease;
}

.quick-start-btn:hover {
  color: #00427e;
  border-color: #dbe9f6;
  box-shadow: 0 6px 16px rgba(255, 255, 255, 0.16);
}

@media (max-width: 900px) {
  .common-nav {
    padding: 0 16px;
  }

  .common-nav-inner {
    flex-wrap: wrap;
    justify-content: center;
    padding: 16px 0;
  }

  .nav-center {
    order: 3;
    width: 100%;
    flex-wrap: wrap;
    gap: 16px;
  }
}
</style>
