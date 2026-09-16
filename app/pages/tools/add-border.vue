<script setup lang="ts">
const PRESETS = ['#161826', '#e9e9ed', '#9184d9', '#ffffff', '#000000', '#3a3f55']

useHead({
  title: 'Add a border to your picture — Image Tools'
})

const img = shallowRef<HTMLImageElement | null>(null)
const imgUrl = ref<string | null>(null)
const drag = ref(false)

const hBorder = ref(24)
const vBorder = ref(24)
const color = ref('#161826')

const inputRef = ref<HTMLInputElement | null>(null)
const canvasRef = ref<HTMLCanvasElement | null>(null)

const ready = computed(() => !!img.value)

function loadFile(file: File | null | undefined) {
  if (!file || !file.type.startsWith('image/')) return
  const url = URL.createObjectURL(file)
  const image = new Image()
  image.onload = () => {
    const prevUrl = imgUrl.value
    img.value = image
    imgUrl.value = url
    if (prevUrl) URL.revokeObjectURL(prevUrl)
  }
  image.src = url
}

function openPicker() {
  inputRef.value?.click()
}

function onFileChange(e: Event) {
  const target = e.target as HTMLInputElement
  loadFile(target.files?.[0])
  target.value = ''
}

function onDrop(e: DragEvent) {
  e.preventDefault()
  drag.value = false
  loadFile(e.dataTransfer?.files?.[0])
}

function onDragOver(e: DragEvent) {
  e.preventDefault()
  drag.value = true
}

function onDragLeave() {
  drag.value = false
}

function clearImage(e: Event) {
  e.stopPropagation()
  const prevUrl = imgUrl.value
  img.value = null
  imgUrl.value = null
  if (prevUrl) URL.revokeObjectURL(prevUrl)
}

function drawPreview() {
  const canvas = canvasRef.value
  if (!canvas) return
  const image = img.value
  if (!image) {
    canvas.width = 0
    canvas.height = 0
    return
  }
  const w = image.naturalWidth, h = image.naturalHeight
  canvas.width = w + hBorder.value * 2
  canvas.height = h + vBorder.value * 2
  const ctx = canvas.getContext('2d')
  if (!ctx) return
  ctx.fillStyle = color.value
  ctx.fillRect(0, 0, canvas.width, canvas.height)
  ctx.drawImage(image, hBorder.value, vBorder.value, w, h)
}

watch([img, hBorder, vBorder, color], drawPreview)

function goFullscreen() {
  const canvas = canvasRef.value as (HTMLCanvasElement & { webkitRequestFullscreen?: () => void }) | null
  if (!canvas) return
  if (canvas.requestFullscreen) canvas.requestFullscreen()
  else if (canvas.webkitRequestFullscreen) canvas.webkitRequestFullscreen()
}

function download() {
  const canvas = canvasRef.value
  if (!canvas || !canvas.width) return
  canvas.toBlob((blob) => {
    if (!blob) return
    const url = URL.createObjectURL(blob)
    const a = document.createElement('a')
    a.href = url
    a.download = 'bordered-image.png'
    document.body.appendChild(a)
    a.click()
    a.remove()
    setTimeout(() => URL.revokeObjectURL(url), 1000)
  }, 'image/png')
}
</script>

<template>
  <div class="wrap-wide">
    <section class="hero">
      <h1 class="display">Add a border to your picture.</h1>
      <p class="sub">Upload an image, set the border width and color, download the result. Everything happens in your browser.</p>
    </section>
  </div>

  <section class="tool-section">
    <div class="wrap-wide">
      <div class="card elev-md tool-card">

        <div class="tool-layout">
          <div class="left-col">
            <div
              class="dropzone"
              :class="{ 'drag-over': drag }"
              @click="openPicker"
              @dragover="onDragOver"
              @dragleave="onDragLeave"
              @drop="onDrop"
            >
              <input ref="inputRef" type="file" accept="image/*" @change="onFileChange" />
              <template v-if="img">
                <img class="thumb" :src="imgUrl!" alt="Uploaded image" />
                <button type="button" class="remove-btn" aria-label="Remove image" @click="clearImage">
                  <svg width="14" height="14" viewBox="0 0 256 256" fill="currentColor"><path d="M205.66,194.34a8,8,0,0,1-11.32,11.32L128,139.31,61.66,205.66a8,8,0,0,1-11.32-11.32L116.69,128,50.34,61.66A8,8,0,0,1,61.66,50.34L128,116.69l66.34-66.35a8,8,0,0,1,11.32,11.32L139.31,128Z" /></svg>
                </button>
              </template>
              <template v-else>
                <svg class="placeholder-icon" width="32" height="32" viewBox="0 0 256 256" fill="currentColor"><path d="M216,40H40A16,16,0,0,0,24,56V200a16,16,0,0,0,16,16H216a16,16,0,0,0,16-16V56A16,16,0,0,0,216,40Zm0,16V158.75l-26.07-26.06a16,16,0,0,0-22.63,0l-20,20-44-44a16,16,0,0,0-22.63,0L40,149.37V56ZM40,200V172l52-52,80,80H40Zm176,0H194.63l-36-36,20-20L216,182.63V200ZM144,100a12,12,0,1,1,12,12A12,12,0,0,1,144,100Z" /></svg>
                <span class="placeholder-text">Drop an image, or click to browse</span>
              </template>
            </div>
          </div>

          <div class="right-col">
            <div class="field">
              <label class="slider-label">Left &amp; right border<span class="val">{{ hBorder }}px</span></label>
              <input type="range" min="0" max="200" v-model.number="hBorder" />
            </div>
            <div class="field">
              <label class="slider-label">Top &amp; bottom border<span class="val">{{ vBorder }}px</span></label>
              <input type="range" min="0" max="200" v-model.number="vBorder" />
            </div>

            <div class="field">
              <label>Border color</label>
              <div class="color-row">
                <input type="color" v-model="color" />
                <div class="swatches">
                  <div
                    v-for="hex in PRESETS"
                    :key="hex"
                    class="swatch"
                    :class="{ selected: color.toLowerCase() === hex }"
                    :style="{ background: hex }"
                    @click="color = hex"
                  ></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="preview-wrap">
          <canvas ref="canvasRef" v-show="ready"></canvas>
          <p v-if="!ready" class="preview-empty">Upload an image to see a preview.</p>
          <button v-if="ready" type="button" class="fullscreen-btn" aria-label="View full screen" @click="goFullscreen">
            <svg width="16" height="16" viewBox="0 0 256 256" fill="currentColor"><path d="M164,56a8,8,0,0,1,8-8h48a8,8,0,0,1,8,8v48a8,8,0,0,1-16,0V75.31l-42.34,42.35a8,8,0,0,1-11.32-11.32L200.69,64H172A8,8,0,0,1,164,56ZM92,192H43.31l42.35-42.34a8,8,0,0,0-11.32-11.32L32,180.69V152a8,8,0,0,0-16,0v48a8,8,0,0,0,8,8H92a8,8,0,0,0,0-16Zm128-40a8,8,0,0,0-8,8v28.69l-42.34-42.35a8,8,0,0,0-11.32,11.32L200.69,200H172a8,8,0,0,0,0,16h48a8,8,0,0,0,8-8V152A8,8,0,0,0,220,152ZM92,32H44a8,8,0,0,0-8,8V88a8,8,0,0,0,16,0V59.31l42.34,42.35a8,8,0,0,0,11.32-11.32L63.31,48H92a8,8,0,0,0,0-16Z" /></svg>
          </button>
        </div>

        <div class="actions">
          <span class="hint">{{ ready ? 'Ready to download' : '' }}</span>
          <button type="button" class="btn btn-primary" :disabled="!ready" @click="download">
            <svg width="14" height="14" viewBox="0 0 256 256" fill="currentColor" style="margin-right: 6px; vertical-align: -2px"><path d="M224,152v56a16,16,0,0,1-16,16H48a16,16,0,0,1-16-16V152a8,8,0,0,1,16,0v56H208V152a8,8,0,0,1,16,0ZM117.66,157.66a8,8,0,0,0,11.32,0l40-40a8,8,0,0,0-11.32-11.32L136,128.69V40a8,8,0,0,0-16,0v88.69L98.34,106.34a8,8,0,0,0-11.32,11.32Z" /></svg>Download
          </button>
        </div>

      </div>
    </div>
  </section>
</template>

<style scoped>
.tool-layout { display: grid; grid-template-columns: minmax(0, 1.1fr) minmax(0, 1fr); gap: var(--space-6); align-items: start; }
@media (max-width: 720px) { .tool-layout { grid-template-columns: 1fr; } }
.left-col, .right-col { display: grid; gap: var(--space-5); }
.dropzone { aspect-ratio: 16 / 9; }
.field label.slider-label { display: flex; justify-content: space-between; align-items: baseline; }
.field label.slider-label span.val { color: color-mix(in srgb, var(--color-text) 60%, transparent); font-weight: 400; }
input[type='range'] { width: 100%; accent-color: var(--color-accent); }
.color-row { display: flex; align-items: center; gap: var(--space-3); }
input[type='color'] {
  width: 44px; height: 36px; border-radius: var(--radius-sm); border: 1px solid var(--color-neutral-600);
  background: var(--color-neutral-800); cursor: pointer; padding: 2px;
}
.swatches { display: flex; gap: var(--space-2); }
.swatch {
  width: 28px; height: 28px; border-radius: 50%; cursor: pointer; border: 2px solid var(--color-neutral-600);
}
.swatch.selected { border-color: var(--color-accent); }
.preview-wrap { position: relative; }
.preview-wrap .fullscreen-btn {
  position: absolute; top: var(--space-2); right: var(--space-2); width: 32px; height: 32px;
  border-radius: 50%; background: color-mix(in srgb, var(--color-neutral-900) 80%, transparent);
  border: 1px solid var(--color-neutral-600); color: var(--color-text);
  display: flex; align-items: center; justify-content: center; cursor: pointer;
}
.preview-wrap .fullscreen-btn:hover { border-color: var(--color-accent); color: var(--color-accent); }
.preview-wrap canvas { border-radius: 0; }
canvas:fullscreen { object-fit: contain; width: 100vw; height: 100vh; background: var(--color-neutral-900); max-width: none; max-height: none; }
canvas:-webkit-full-screen { object-fit: contain; width: 100vw; height: 100vh; background: var(--color-neutral-900); max-width: none; max-height: none; }
</style>
