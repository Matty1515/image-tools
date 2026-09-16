<script setup lang="ts">
type Slot = 1 | 2
type Orientation = 'horizontal' | 'vertical'
type Adapt = 'smaller' | 'larger'
type Format = 'png' | 'jpeg'

useHead({
  title: 'Combine two pictures into one — Image Tools'
})

const img1 = shallowRef<HTMLImageElement | null>(null)
const img2 = shallowRef<HTMLImageElement | null>(null)
const img1Url = ref<string | null>(null)
const img2Url = ref<string | null>(null)
const drag1 = ref(false)
const drag2 = ref(false)

const orientation = ref<Orientation>('horizontal')
const adapt = ref<Adapt>('smaller')
const format = ref<Format>('png')

const input1Ref = ref<HTMLInputElement | null>(null)
const input2Ref = ref<HTMLInputElement | null>(null)
const canvasRef = ref<HTMLCanvasElement | null>(null)

const ready = computed(() => !!(img1.value && img2.value))

function imgRef(slot: Slot) {
  return slot === 1 ? img1 : img2
}
function urlRef(slot: Slot) {
  return slot === 1 ? img1Url : img2Url
}

function loadFile(slot: Slot, file: File | null | undefined) {
  if (!file || !file.type.startsWith('image/')) return
  const url = URL.createObjectURL(file)
  const img = new Image()
  img.onload = () => {
    const prevUrl = urlRef(slot).value
    imgRef(slot).value = img
    urlRef(slot).value = url
    if (prevUrl) URL.revokeObjectURL(prevUrl)
  }
  img.src = url
}

function openPicker(slot: Slot) {
  ;(slot === 1 ? input1Ref : input2Ref).value?.click()
}

function onFileChange(slot: Slot, e: Event) {
  const target = e.target as HTMLInputElement
  loadFile(slot, target.files?.[0])
  target.value = ''
}

function onDrop(slot: Slot, e: DragEvent) {
  e.preventDefault()
  ;(slot === 1 ? drag1 : drag2).value = false
  loadFile(slot, e.dataTransfer?.files?.[0])
}

function onDragOver(slot: Slot, e: DragEvent) {
  e.preventDefault()
  ;(slot === 1 ? drag1 : drag2).value = true
}

function onDragLeave(slot: Slot) {
  ;(slot === 1 ? drag1 : drag2).value = false
}

function clearImage(slot: Slot, e: Event) {
  e.stopPropagation()
  const prevUrl = urlRef(slot).value
  imgRef(slot).value = null
  urlRef(slot).value = null
  if (prevUrl) URL.revokeObjectURL(prevUrl)
}

function drawPreview() {
  const canvas = canvasRef.value
  if (!canvas) return
  const i1 = img1.value
  const i2 = img2.value
  if (!i1 || !i2) {
    canvas.width = 0
    canvas.height = 0
    return
  }
  const w1 = i1.naturalWidth, h1 = i1.naturalHeight
  const w2 = i2.naturalWidth, h2 = i2.naturalHeight
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  if (orientation.value === 'horizontal') {
    const target = adapt.value === 'smaller' ? Math.max(h1, h2) : Math.min(h1, h2)
    const s1 = target / h1, s2 = target / h2
    const dw1 = w1 * s1, dw2 = w2 * s2
    canvas.width = Math.round(dw1 + dw2)
    canvas.height = Math.round(target)
    ctx.clearRect(0, 0, canvas.width, canvas.height)
    ctx.drawImage(i1, 0, 0, dw1, target)
    ctx.drawImage(i2, dw1, 0, dw2, target)
  } else {
    const target = adapt.value === 'smaller' ? Math.max(w1, w2) : Math.min(w1, w2)
    const s1 = target / w1, s2 = target / w2
    const dh1 = h1 * s1, dh2 = h2 * s2
    canvas.width = Math.round(target)
    canvas.height = Math.round(dh1 + dh2)
    ctx.clearRect(0, 0, canvas.width, canvas.height)
    ctx.drawImage(i1, 0, 0, target, dh1)
    ctx.drawImage(i2, 0, dh1, target, dh2)
  }
}

watch([img1, img2, orientation, adapt], drawPreview)

function download() {
  const canvas = canvasRef.value
  if (!canvas || !canvas.width) return
  const mime = format.value === 'jpeg' ? 'image/jpeg' : 'image/png'
  const ext = format.value === 'jpeg' ? 'jpg' : 'png'
  canvas.toBlob(
    (blob) => {
      if (!blob) return
      const url = URL.createObjectURL(blob)
      const a = document.createElement('a')
      a.href = url
      a.download = `combined-image.${ext}`
      document.body.appendChild(a)
      a.click()
      a.remove()
      setTimeout(() => URL.revokeObjectURL(url), 1000)
    },
    mime,
    format.value === 'jpeg' ? 0.92 : undefined
  )
}
</script>

<template>
  <div class="wrap-wide">
    <section class="hero">
      <h1 class="display">Combine two pictures into one.</h1>
      <p class="sub">Upload two images, choose a layout, download the result. Everything happens in your browser — nothing is uploaded anywhere.</p>
    </section>
  </div>

  <section class="tool-section" id="tool">
    <div class="wrap-wide">
      <div class="card elev-md tool-card">

        <div class="uploads">
          <div
            class="dropzone"
            :class="{ 'drag-over': drag1 }"
            @click="openPicker(1)"
            @dragover="onDragOver(1, $event)"
            @dragleave="onDragLeave(1)"
            @drop="onDrop(1, $event)"
          >
            <input ref="input1Ref" type="file" accept="image/*" @change="onFileChange(1, $event)" />
            <template v-if="img1">
              <img class="thumb" :src="img1Url!" alt="First image" />
              <button type="button" class="remove-btn" aria-label="Remove first image" @click="clearImage(1, $event)">
                <svg width="14" height="14" viewBox="0 0 256 256" fill="currentColor"><path d="M205.66,194.34a8,8,0,0,1-11.32,11.32L128,139.31,61.66,205.66a8,8,0,0,1-11.32-11.32L116.69,128,50.34,61.66A8,8,0,0,1,61.66,50.34L128,116.69l66.34-66.35a8,8,0,0,1,11.32,11.32L139.31,128Z" /></svg>
              </button>
            </template>
            <template v-else>
              <svg class="placeholder-icon" width="32" height="32" viewBox="0 0 256 256" fill="currentColor"><path d="M216,40H40A16,16,0,0,0,24,56V200a16,16,0,0,0,16,16H216a16,16,0,0,0,16-16V56A16,16,0,0,0,216,40Zm0,16V158.75l-26.07-26.06a16,16,0,0,0-22.63,0l-20,20-44-44a16,16,0,0,0-22.63,0L40,149.37V56ZM40,200V172l52-52,80,80H40Zm176,0H194.63l-36-36,20-20L216,182.63V200ZM144,100a12,12,0,1,1,12,12A12,12,0,0,1,144,100Z" /></svg>
              <span class="placeholder-text">Drop first image, or click to browse</span>
            </template>
          </div>

          <div
            class="dropzone"
            :class="{ 'drag-over': drag2 }"
            @click="openPicker(2)"
            @dragover="onDragOver(2, $event)"
            @dragleave="onDragLeave(2)"
            @drop="onDrop(2, $event)"
          >
            <input ref="input2Ref" type="file" accept="image/*" @change="onFileChange(2, $event)" />
            <template v-if="img2">
              <img class="thumb" :src="img2Url!" alt="Second image" />
              <button type="button" class="remove-btn" aria-label="Remove second image" @click="clearImage(2, $event)">
                <svg width="14" height="14" viewBox="0 0 256 256" fill="currentColor"><path d="M205.66,194.34a8,8,0,0,1-11.32,11.32L128,139.31,61.66,205.66a8,8,0,0,1-11.32-11.32L116.69,128,50.34,61.66A8,8,0,0,1,61.66,50.34L128,116.69l66.34-66.35a8,8,0,0,1,11.32,11.32L139.31,128Z" /></svg>
              </button>
            </template>
            <template v-else>
              <svg class="placeholder-icon" width="32" height="32" viewBox="0 0 256 256" fill="currentColor"><path d="M216,40H40A16,16,0,0,0,24,56V200a16,16,0,0,0,16,16H216a16,16,0,0,0,16-16V56A16,16,0,0,0,216,40Zm0,16V158.75l-26.07-26.06a16,16,0,0,0-22.63,0l-20,20-44-44a16,16,0,0,0-22.63,0L40,149.37V56ZM40,200V172l52-52,80,80H40Zm176,0H194.63l-36-36,20-20L216,182.63V200ZM144,100a12,12,0,1,1,12,12A12,12,0,0,1,144,100Z" /></svg>
              <span class="placeholder-text">Drop second image, or click to browse</span>
            </template>
          </div>
        </div>

        <div class="controls-row">
          <div class="field">
            <label id="layout-label">Layout</label>
            <div class="seg" role="radiogroup" aria-labelledby="layout-label">
              <label class="seg-opt"><input type="radio" name="orientation" value="horizontal" v-model="orientation" />Side by side</label>
              <label class="seg-opt"><input type="radio" name="orientation" value="vertical" v-model="orientation" />Stacked</label>
            </div>
          </div>
          <div class="field">
            <label id="format-label">Format</label>
            <div class="seg" role="radiogroup" aria-labelledby="format-label">
              <label class="seg-opt"><input type="radio" name="format" value="png" v-model="format" />PNG</label>
              <label class="seg-opt"><input type="radio" name="format" value="jpeg" v-model="format" />JPEG</label>
            </div>
          </div>
        </div>

        <div class="field">
          <label id="adapt-label">Sizing</label>
          <div class="stack" style="display: grid; gap: var(--space-1)" role="radiogroup" aria-labelledby="adapt-label">
            <label class="radio"><input type="radio" name="adapt" value="smaller" v-model="adapt" /><span class="dot"></span>Smaller picture adapts to larger (default)</label>
            <label class="radio"><input type="radio" name="adapt" value="larger" v-model="adapt" /><span class="dot"></span>Larger picture adapts to smaller</label>
          </div>
        </div>

        <div class="preview-wrap">
          <canvas ref="canvasRef" v-show="ready"></canvas>
          <p v-if="!ready" class="preview-empty">Upload both images to see a preview.</p>
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
.uploads { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: var(--space-4); }
@media (max-width: 560px) { .uploads { grid-template-columns: 1fr; } }
.dropzone { aspect-ratio: 4 / 3; }
.controls-row { display: flex; gap: var(--space-5) var(--space-6); flex-wrap: wrap; }
.controls-row .field { flex: 1 1 200px; min-width: 0; }
.preview-wrap canvas { border-radius: var(--radius-sm); }
</style>
