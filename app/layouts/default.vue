<template>
  <div class="page">
    <nav class="nav">
      <NuxtLink to="/" class="nav-brand">
        <svg class="brand-mark" width="20" height="20" viewBox="0 0 256 256" fill="none" xmlns="http://www.w3.org/2000/svg">
          <rect x="24" y="60" width="120" height="136" rx="14" fill="currentColor" opacity="0.55" />
          <rect x="112" y="60" width="120" height="136" rx="14" stroke="currentColor" stroke-width="14" />
        </svg>
        Image Tools
      </NuxtLink>
    </nav>

    <slot />

    <div class="wrap-wide">
      <hr class="rule" />
      <footer class="note">All processing happens locally in your browser. Nothing is uploaded to a server.</footer>
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  background:
    radial-gradient(1200px 700px at 88% -160px, color-mix(in srgb, var(--color-accent-900) 75%, transparent), transparent 60%),
    radial-gradient(900px 700px at -10% 40%, color-mix(in srgb, var(--color-accent-2-900) 45%, transparent), transparent 55%),
    radial-gradient(900px 700px at 50% 120%, color-mix(in srgb, black 30%, transparent), transparent 55%),
    var(--color-bg);
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: 100% 100%;
}
.nav { padding-inline: max(clamp(20px, 5vw, 56px), calc((100% - 1080px) / 2 + clamp(20px, 5vw, 56px))); }
.nav-brand,
.nav-brand[aria-current='page'] {
  display: inline-flex; align-items: center; gap: var(--space-2);
  font-size: 18px; color: inherit; text-decoration: none;
}
.brand-mark { color: var(--color-accent); flex-shrink: 0; }
footer.note { padding: var(--space-6) 0 var(--space-8); font-size: 13px; line-height: 1.6; color: color-mix(in srgb, var(--color-text) 55%, transparent); }
</style>
