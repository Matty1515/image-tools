<template>
  <div class="page">
    <nav class="nav">
      <NuxtLink to="/" class="nav-brand">
        <img class="brand-mark" src="/image-tools-logo.png" alt="" width="27" height="25" />
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
.brand-mark { width: auto; height: 18px; flex-shrink: 0; object-fit: contain; }
footer.note { padding: var(--space-6) 0 var(--space-8); font-size: 13px; line-height: 1.6; color: color-mix(in srgb, var(--color-text) 55%, transparent); }
</style>
