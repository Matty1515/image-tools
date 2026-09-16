<script setup lang="ts">
useHead({
  title: 'Image Tools'
})
</script>

<template>
  <div class="wrap-wide">
    <section class="hero">
      <div class="hero-glow"></div>
      <h1 class="display">Small, focused tools <span class="accent-word">for everyday images.</span></h1>
      <p class="sub">No accounts, no uploads to a server — everything runs right in your browser. Pick a tool below to get started.</p>
    </section>
  </div>

  <section class="tools-section">
    <div class="wrap-wide">
      <div class="tools-grid">

        <NuxtLink class="tool-card featured live" to="/tools/combine-two-pictures">
          <div class="card-sheen"></div>
          <div class="card-icon-wrap">
            <svg viewBox="0 0 256 256" fill="currentColor"><path d="M208,24H48A16,16,0,0,0,32,40V216a16,16,0,0,0,16,16H208a16,16,0,0,0,16-16V40A16,16,0,0,0,208,24ZM120,216H48V40h72Zm88,0H136V40h72Z" /></svg>
          </div>
          <h3>Merge Tool</h3>
          <p class="desc">Combine two pictures into one — choose a side-by-side or stacked layout and download the result.</p>
          <span class="card-cta">Open tool
            <svg viewBox="0 0 256 256" fill="currentColor"><path d="M221.66,133.66l-72,72a8,8,0,0,1-11.32-11.32L196.69,136H40a8,8,0,0,1,0-16H196.69L138.34,61.66a8,8,0,0,1,11.32-11.32l72,72A8,8,0,0,1,221.66,133.66Z" /></svg>
          </span>
        </NuxtLink>

        <div class="tool-card soon">
          <div class="card-icon-wrap">
            <svg viewBox="0 0 256 256" fill="currentColor"><path d="M216,40H40A16,16,0,0,0,24,56V200a16,16,0,0,0,16,16H216a16,16,0,0,0,16-16V56A16,16,0,0,0,216,40Zm0,16v92.69l-27.35-27.36a16,16,0,0,0-22.63,0L143.31,144H40V56ZM40,200V166.63l68-68,80,80H40Zm176,0H211.31l-32-32,20.35-20.34L216,164.69V200Z" /></svg>
          </div>
          <h3>Border Tool</h3>
          <p class="desc">Add a clean border or frame around any photo, with custom width and color.</p>
          <span class="soon-badge">Coming soon</span>
        </div>

        <div class="tool-card soon">
          <div class="card-icon-wrap">
            <svg viewBox="0 0 256 256" fill="currentColor"><path d="M227.31,73.37,182.63,28.69a16,16,0,0,0-22.63,0L36.69,152A15.86,15.86,0,0,0,32,163.31V208a16,16,0,0,0,16,16H92.69A15.86,15.86,0,0,0,104,219.31L227.31,96A16,16,0,0,0,227.31,73.37ZM92.69,208H48V163.31l88-88L180.69,120ZM192,108.68,147.31,64,171,40.32,215.68,85Z" /></svg>
          </div>
          <h3>Edit Tool</h3>
          <p class="desc">Crop, rotate and adjust your images with simple, precise controls.</p>
          <span class="soon-badge">Coming soon</span>
        </div>

        <div class="tool-card soon">
          <div class="card-icon-wrap">
            <svg viewBox="0 0 256 256" fill="currentColor"><path d="M128,24A104,104,0,1,0,232,128,104.12,104.12,0,0,0,128,24Zm0,192a88,88,0,0,1-88-88A87.62,87.62,0,0,1,63.6,74.9c-.36,2.34-.6,4.72-.6,7.1a64,64,0,0,0,64,64c1.62,0,3.22-.13,4.8-.3A40,40,0,0,0,176,184a39.52,39.52,0,0,0-2.24-13.06A87.94,87.94,0,0,1,128,216Z" /></svg>
          </div>
          <h3>Opacity Tool</h3>
          <p class="desc">Fine-tune image transparency and export with a clean alpha channel.</p>
          <span class="soon-badge">Coming soon</span>
        </div>

      </div>
    </div>
  </section>
</template>

<style scoped>
.hero { padding: clamp(56px, 10vw, 108px) 0 clamp(40px, 6vw, 64px); position: relative; }
.hero-glow {
  position: absolute; top: -40px; left: 50%; width: 640px; height: 320px; transform: translateX(-50%);
  background: radial-gradient(closest-side, color-mix(in srgb, var(--color-accent) 22%, transparent), transparent);
  filter: blur(10px); pointer-events: none; z-index: 0;
}
.display { position: relative; z-index: 1; font-size: clamp(38px, 6vw, 64px); max-width: 20ch; }
.display .accent-word { color: var(--color-accent-300); }
.hero .sub { position: relative; z-index: 1; font-size: 18px; max-width: 56ch; margin: var(--space-4) 0 0; }

.tools-section { padding: 0 0 clamp(56px, 8vw, 96px); }
.tools-grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: var(--space-6); margin-top: var(--space-6); }
@media (max-width: 680px) { .tools-grid { grid-template-columns: 1fr; } }

.tool-card {
  position: relative; display: block; text-decoration: none; color: inherit;
  padding: var(--space-8); overflow: hidden; isolation: isolate;
  border: 1px solid var(--color-neutral-800);
  border-radius: var(--radius-lg);
  transition: transform 0.18s ease, border-color 0.18s ease, box-shadow 0.18s ease, background 0.18s ease;
}
.tool-card.live { cursor: pointer; }
.tool-card.live:hover {
  transform: translateY(-3px);
  border-color: color-mix(in srgb, var(--color-accent) 65%, var(--color-neutral-600));
  background: color-mix(in srgb, var(--color-accent-800) 16%, var(--color-neutral-800));
  box-shadow: var(--shadow-sm);
}
.tool-card.live:focus-visible { outline: 2px solid var(--color-accent); outline-offset: 2px; }
.tool-card .card-icon-wrap {
  width: 48px; height: 48px; border-radius: var(--radius-md); display: flex; align-items: center; justify-content: center;
  background: color-mix(in srgb, var(--color-accent-800) 55%, var(--color-neutral-800)); color: var(--color-accent-300);
  margin-bottom: var(--space-4); position: relative; z-index: 1;
}
.tool-card .card-icon-wrap svg { width: 24px; height: 24px; }
.tool-card h3 { font-family: var(--font-heading); font-weight: var(--font-heading-weight); font-size: 20px; margin: 0 0 var(--space-2); position: relative; z-index: 1; }
.tool-card p.desc { font-size: 14px; line-height: 1.55; color: color-mix(in srgb, var(--color-text) 68%, transparent); margin: 0 0 var(--space-4); position: relative; z-index: 1; max-width: 42ch; }
.tool-card .card-cta { display: inline-flex; align-items: center; gap: var(--space-2); font-size: 13px; color: var(--color-accent-300); font-weight: 500; position: relative; z-index: 1; }
.tool-card .card-cta svg { width: 14px; height: 14px; transition: transform 0.18s ease; }
.tool-card.live:hover .card-cta svg { transform: translateX(3px); }
.tool-card .card-sheen {
  position: absolute; inset: -1px; z-index: 0; opacity: 0; transition: opacity 0.25s ease;
  background: radial-gradient(360px 220px at 85% -20%, color-mix(in srgb, var(--color-accent) 28%, transparent), transparent 70%);
}
.tool-card.live:hover .card-sheen { opacity: 1; }
.tool-card.live:hover .card-icon-wrap { background: color-mix(in srgb, var(--color-accent-600) 55%, var(--color-accent-800)); color: var(--color-text); }

.tool-card.soon { opacity: 0.5; display: flex; flex-direction: column; }
.tool-card.soon .card-icon-wrap { background: var(--color-neutral-800); color: var(--color-neutral-400); }
.tool-card.soon p.desc { flex: 1 0 auto; }
.soon-badge {
  display: inline-flex; align-self: flex-start; position: relative; z-index: 1;
  font-size: 11px; letter-spacing: 0.05em; text-transform: uppercase; font-weight: 500;
  color: var(--color-neutral-400); border: 1px solid var(--color-neutral-600);
  padding: 3px 9px; border-radius: var(--radius-sm); background: color-mix(in srgb, var(--color-neutral-900) 60%, transparent);
}
.tool-card.featured .card-cta { border: 1px solid var(--color-accent-600); border-radius: var(--radius-md); padding: 10px 18px; align-self: flex-start; }
</style>
