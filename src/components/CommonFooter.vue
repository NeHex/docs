<template>
  <footer class="common-footer">
    <div class="common-footer-inner">
      <a class="footer-brand" href="/">
        <img class="footer-logo" src="/images/android-chrome-192x192.png" alt="NeHex Logo" />
        <span class="footer-name">NeHex</span>
      </a>

      <p class="footer-meta">© {{ new Date().getFullYear() }} NeHex. All rights reserved.</p>
    </div>
  </footer>
</template>

<style scoped>
.common-footer {
  width: 100%;
  padding: 22px 24px 30px;
  border-top: 1px solid rgba(167, 191, 223, 0.14);
  background: rgba(10, 12, 16, 0.75);
  backdrop-filter: blur(8px);
}

.common-footer-inner {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  flex-wrap: wrap;
}

.footer-brand {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
}

.footer-logo {
  width: 26px;
  height: 26px;
  object-fit: contain;
}

.footer-name {
  font-size: 18px;
  font-weight: 800;
  line-height: 1;
  background: linear-gradient(90deg, #86b8ff 0%, #6fe8ff 100%);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.footer-meta {
  color: rgba(225, 234, 247, 0.6);
  font-size: 13px;
  line-height: 1.4;
}

@media (max-width: 900px) {
  .common-footer {
    padding: 18px 16px 24px;
  }

  .common-footer-inner {
    justify-content: center;
    text-align: center;
  }
}
</style>
