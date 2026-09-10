<script setup lang="ts">
import { nextTick, onBeforeUnmount, onMounted, ref, watch } from "vue";

import {
  createDroplets,
  supportsHtmlInCanvas,
  type DropletsInstance,
  type DropletsOptions,
} from "./DropletsVanilla";

const props = defineProps<DropletsOptions>();

const sourceEl = ref<HTMLCanvasElement | null>(null);
const contentEl = ref<HTMLDivElement | null>(null);
const outputEl = ref<HTMLCanvasElement | null>(null);
const native = ref(false);

let instance: DropletsInstance | null = null;
let disposed = false;

async function mountDroplets() {
  await nextTick();
  if (disposed || !native.value) return;
  if (!sourceEl.value || !contentEl.value || !outputEl.value) return;

  instance = createDroplets(
    {
      source: sourceEl.value,
      content: contentEl.value,
      output: outputEl.value,
    },
    props,
  );

  if (!instance) native.value = false;
}

onMounted(async () => {
  native.value = supportsHtmlInCanvas();
  await mountDroplets();
});

onBeforeUnmount(() => {
  disposed = true;
  instance?.destroy();
  instance = null;
});

watch(
  () => ({ ...props }),
  (next) => instance?.setOptions(next),
  { deep: true },
);
</script>

<template>
  <div class="droplets-root">
    <canvas
      ref="sourceEl"
      layoutsubtree="true"
      :style="
        native
          ? 'position: absolute; inset: 0; width: 100%; height: 100%'
          : 'display: none'
      "
    >
      <div
        v-if="native"
        ref="contentEl"
        class="droplets-content"
      >
        <slot />
      </div>
    </canvas>

    <div
      v-if="!native"
      ref="contentEl"
      class="droplets-content"
    >
      <slot />
    </div>

    <canvas
      v-if="native"
      ref="outputEl"
      aria-hidden="true"
      class="droplets-output"
    />
  </div>
</template>

<style scoped>
.droplets-root {
  position: relative;
}

.droplets-content {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: auto;
}

.droplets-output {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>