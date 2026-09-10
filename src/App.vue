<script setup lang="ts">
import { computed, markRaw, ref } from 'vue'
import Droplets from './components/canvasui/Droplets.vue'
import Ripple from './components/canvasui/Ripple.vue'
import Shatter from './components/canvasui/Shatter.vue'

type EffectKey = 'droplets' | 'shatter' | 'ripple'

const effects = [
  { key: 'droplets' as const, label: 'Rain on glass', kicker: '01 / DROPLETS', component: markRaw(Droplets), description: 'Pointer wipes leave a clear trail across the wet glass.' },
  { key: 'shatter' as const, label: 'Glass fracture', kicker: '02 / SHATTER', component: markRaw(Shatter), description: 'Move across the surface to lift the page into floating glass shards.' },
  { key: 'ripple' as const, label: 'Water ripple', kicker: '03 / RIPPLE', component: markRaw(Ripple), description: 'Click the surface and watch concentric waves bend the page like water.' },
]

const selectedEffect = ref<EffectKey>('droplets')
const intensity = ref(0.8)
const speed = ref(1)
const rippleAmplitude = ref(0.5)
const rippleSpeed = ref(0.65)
const rippleWavelength = ref(80)
const rippleRings = ref(2)
const rippleDecay = ref(1)
const rippleRefraction = ref(100)
const rippleDispersion = ref(0.5)
const rippleShine = ref(0.5)
const rippleTrigger = ref<'click' | 'hover' | 'none'>('click')
const rippleInterval = ref(0)
const activeEffect = computed(() => effects.find((effect) => effect.key === selectedEffect.value) ?? effects[0])

function selectEffect(key: EffectKey) {
  selectedEffect.value = key
}
</script>

<template>
  <component
    :is="activeEffect.component"
    :key="activeEffect.key"
    class="canvas-demo"
    style="width: 100vw; height: 100vh"
    v-bind="selectedEffect === 'droplets' ? {
      intensity,
      speed,
      scale: 0.5,
      dropWidth: 1,
      dropLength: 1.2,
      refraction: 0.8,
      blur: 0.4,
      interactive: true,
    } : selectedEffect === 'ripple' ? {
      amplitude: rippleAmplitude,
      speed: rippleSpeed,
      wavelength: rippleWavelength,
      rings: rippleRings,
      decay: rippleDecay,
      refraction: rippleRefraction,
      dispersion: rippleDispersion,
      shine: rippleShine,
      trigger: rippleTrigger,
      interval: rippleInterval,
    } : {
      radius: 0.42,
      softness: 0.58,
      tileSize: 125,
      shards: 1,
      lift: 34,
      tilt: 2,
      scatter: 7,
      perspective: 1500,
      shadow: 0.55,
      shading: 0.6,
      refraction: 1.5,
      dispersion: 0.3,
      floatSpeed: 2,
      strength: 1,
      followSpeed: 3,
    }"
  >
    <main class="canvas-demo-content">
      <nav class="effect-nav" aria-label="Animation effects">
        <p class="nav-label">CanvasUI / Effects</p>
        <button
          v-for="effect in effects"
          :key="effect.key"
          class="effect-link"
          :class="{ active: selectedEffect === effect.key }"
          type="button"
          :aria-current="selectedEffect === effect.key ? 'page' : undefined"
          @click="selectEffect(effect.key)"
        >
          <span class="effect-number">{{ effect.kicker.split(' / ')[0] }}</span>
          <span>{{ effect.label }}</span>
          <span class="effect-arrow">↗</span>
        </button>
      </nav>

      <section class="hero-copy">
        <p class="eyebrow">VUE + WEBGL / {{ activeEffect.kicker.split(' / ')[0] }}</p>
        <h1>{{ activeEffect.label.split(' ')[0] }}<br /><em>{{ activeEffect.label.split(' ').slice(1).join(' ') }}</em></h1>
        <p class="intro">{{ activeEffect.description }}</p>
        <div class="status-line"><span /> LIVE EFFECT FIELD</div>
      </section>

      <aside class="control-panel" aria-label="Animation controls">
        <div class="panel-heading">
          <span>{{ activeEffect.kicker.split(' / ')[1] }}</span>
          <strong>{{ activeEffect.kicker.split(' / ')[0] }}</strong>
        </div>
        <label v-if="selectedEffect === 'droplets'">
          <span>Intensity <output>{{ intensity.toFixed(2) }}</output></span>
          <input v-model.number="intensity" type="range" min="0" max="1.25" step="0.05" />
        </label>
        <label v-if="selectedEffect === 'droplets'">
          <span>Fall speed <output>{{ speed.toFixed(2) }}</output></span>
          <input v-model.number="speed" type="range" min="0" max="3" step="0.1" />
        </label>
        <template v-if="selectedEffect === 'ripple'">
          <label>
            <span>Trigger</span>
            <select v-model="rippleTrigger">
              <option value="click">Click</option>
              <option value="hover">Hover</option>
              <option value="none">None</option>
            </select>
          </label>
          <label>
            <span>Amplitude <output>{{ rippleAmplitude.toFixed(2) }}</output></span>
            <input v-model.number="rippleAmplitude" type="range" min="0" max="3" step="0.05" />
          </label>
          <label>
            <span>Speed <output>{{ rippleSpeed.toFixed(2) }}</output></span>
            <input v-model.number="rippleSpeed" type="range" min="0.2" max="3" step="0.05" />
          </label>
          <label>
            <span>Wavelength <output>{{ rippleWavelength }}</output></span>
            <input v-model.number="rippleWavelength" type="range" min="8" max="160" step="1" />
          </label>
          <label>
            <span>Rings <output>{{ rippleRings }}</output></span>
            <input v-model.number="rippleRings" type="range" min="1" max="8" step="1" />
          </label>
          <label>
            <span>Decay <output>{{ rippleDecay.toFixed(2) }}</output></span>
            <input v-model.number="rippleDecay" type="range" min="0.2" max="3" step="0.05" />
          </label>
          <label>
            <span>Refraction <output>{{ rippleRefraction }}</output></span>
            <input v-model.number="rippleRefraction" type="range" min="0" max="160" step="2" />
          </label>
          <label>
            <span>Dispersion <output>{{ rippleDispersion.toFixed(2) }}</output></span>
            <input v-model.number="rippleDispersion" type="range" min="0" max="1" step="0.02" />
          </label>
          <label>
            <span>Shine <output>{{ rippleShine.toFixed(2) }}</output></span>
            <input v-model.number="rippleShine" type="range" min="0" max="2" step="0.05" />
          </label>
          <label>
            <span>Ambient interval <output>{{ rippleInterval.toFixed(2) }}s</output></span>
            <input v-model.number="rippleInterval" type="range" min="0" max="5" step="0.25" />
          </label>
        </template>
        <p class="hint">{{ activeEffect.description }} The animation is rendered by the CanvasUI WebGL core.</p>
      </aside>
    </main>
  </component>
</template>

<style scoped>
.canvas-demo {
  min-height: 100vh;
  color: #f5f1e8;
  background: #101b20;
}

.canvas-demo-content {
  position: relative;
  display: grid;
  grid-template-columns: 190px minmax(0, 1fr) minmax(260px, 340px);
  align-items: end;
  gap: clamp(32px, 9vw, 160px);
  width: 100%;
  min-height: 100%;
  padding: clamp(32px, 8vw, 110px);
  background:
    linear-gradient(115deg, rgb(11 28 34 / 78%), rgb(11 28 34 / 16%)),
    radial-gradient(circle at 70% 20%, rgb(209 164 91 / 32%), transparent 28%),
    linear-gradient(160deg, #102f36 0%, #315b60 48%, #d2a65c 130%);
}

.effect-nav {
  align-self: start;
  display: grid;
  gap: 8px;
  padding-top: 3px;
}

.nav-label { margin: 0 0 18px; color: rgb(245 241 232 / 42%); font-size: 10px; letter-spacing: 0.12em; text-transform: uppercase; }
.effect-link { display: grid; grid-template-columns: 26px 1fr 18px; align-items: center; gap: 8px; min-height: 42px; padding: 0 10px; border: 0; border-left: 1px solid rgb(245 241 232 / 18%); color: rgb(245 241 232 / 52%); background: transparent; font: inherit; font-size: 12px; text-align: left; cursor: pointer; transition: color 180ms ease, border-color 180ms ease, background 180ms ease; }
.effect-link:hover, .effect-link.active { border-color: #e5b76d; color: #f5f1e8; background: rgb(10 27 32 / 24%); }
.effect-link.active { font-weight: 600; }
.effect-number { color: #e5b76d; font-size: 10px; }
.effect-arrow { opacity: 0; color: #e5b76d; transition: opacity 180ms ease; }
.effect-link:hover .effect-arrow, .effect-link.active .effect-arrow { opacity: 1; }

.hero-copy { max-width: 680px; }
.eyebrow, .status-line, .panel-heading, label { font-size: 11px; letter-spacing: 0.12em; text-transform: uppercase; }
.eyebrow { margin: 0 0 22px; color: #e5b76d; }
h1 { margin: 0; font-family: Georgia, serif; font-size: clamp(58px, 10vw, 144px); font-weight: 400; line-height: 0.82; letter-spacing: -0.04em; }
h1 em { color: #e5b76d; font-weight: 400; }
.intro { max-width: 360px; margin: 36px 0 24px; color: rgb(245 241 232 / 75%); font-size: 16px; line-height: 1.6; }
.status-line { display: flex; align-items: center; gap: 10px; color: rgb(245 241 232 / 58%); }
.status-line span { width: 7px; height: 7px; border-radius: 50%; background: #e5b76d; box-shadow: 0 0 12px #e5b76d; }
.control-panel { padding: 22px; border: 1px solid rgb(245 241 232 / 28%); background: rgb(10 27 32 / 42%); backdrop-filter: blur(14px); }
.panel-heading { display: flex; justify-content: space-between; padding-bottom: 18px; border-bottom: 1px solid rgb(245 241 232 / 18%); color: #e5b76d; }
.panel-heading strong { color: #f5f1e8; font-weight: 400; }

@media (max-width: 900px) {
  .canvas-demo-content { grid-template-columns: 150px minmax(0, 1fr); gap: 28px; }
  .control-panel { grid-column: 2; }
}

@media (max-width: 620px) {
  .canvas-demo-content { grid-template-columns: 1fr; align-content: space-between; gap: 28px; padding: 24px; }
  .effect-nav { display: flex; overflow-x: auto; margin: -4px -24px 0; padding: 0 24px 4px; }
  .nav-label { display: none; }
  .effect-link { flex: 0 0 auto; min-width: 142px; border-top: 1px solid rgb(245 241 232 / 18%); border-left: 0; }
  .control-panel { grid-column: auto; }
  h1 { font-size: clamp(56px, 18vw, 92px); }
}
label { display: grid; gap: 10px; margin-top: 22px; color: rgb(245 241 232 / 72%); }
label span { display: flex; justify-content: space-between; }
output { color: #e5b76d; }
select, input { width: 100%; accent-color: #e5b76d; }
select { padding: 9px; border: 1px solid rgb(245 241 232 / 25%); color: #f5f1e8; background: rgb(245 241 232 / 8%); }
.hint { margin: 24px 0 0; color: rgb(245 241 232 / 48%); font-size: 12px; line-height: 1.5; }

@media (max-width: 700px) {
  .canvas-demo-content { grid-template-columns: 1fr; align-content: end; padding: 28px; }
  h1 { font-size: clamp(58px, 18vw, 100px); }
  .intro { margin: 24px 0 18px; }
  .control-panel { max-width: 100%; }
}
</style>
