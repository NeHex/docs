<script setup lang="ts">
import { RouterView } from 'vue-router'
import CommonNav from './components/CommonNav.vue'
import CommonFooter from './components/CommonFooter.vue'
</script>

<template>
  <div class="app-shell">
    <CommonNav />
    <main class="app-main">
      <RouterView />
    </main>
    <CommonFooter />
  </div>
</template>

<style scoped>
.app-shell {
  min-height: 100vh;
}

.app-main {
  padding: 0;
}
</style>
