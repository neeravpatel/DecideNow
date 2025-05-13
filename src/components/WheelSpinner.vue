<template>
  <div
    class="flex flex-col items-center justify-center min-h-screen bg-gray-100 text-gray-800"
  >
    <div class="flex flex-col items-center gap-4 relative w-fit">
      <!-- Pointer -->
      <div class="absolute top-1/2 right-[-20px] translate-y-[-50%] z-10">
        <div
          class="w-0 h-0 border-l-[12px] border-r-[12px] border-b-[20px] border-l-transparent border-r-transparent border-b-red-500"
          style="transform: rotate(-90deg)"
        ></div>
      </div>

      <!-- Wheel -->
      <canvas
        ref="canvasRef"
        :width="size"
        :height="size"
        class="rounded-full shadow-lg"
      ></canvas>
    </div>

    <!-- Spin Button -->
    <div class="mt-4">
      <button
        @click="spin"
        class="px-4 py-2 bg-blue-600 text-white rounded-xl hover:bg-blue-700 transition"
      >
        🎯 Spin
      </button>
    </div>

    <!-- Selected Item Text -->
    <div v-if="selected" class="mt-4">
      <p class="text-xl font-semibold text-green-600">
        Selected: {{ selected }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from "vue";

interface Props {
  items: string[];
  size?: number;
}

const props = withDefaults(defineProps<Props>(), {
  size: 300,
});

const canvasRef = ref<HTMLCanvasElement | null>(null);
const selected = ref<string | null>(null);
const angle = ref(0);

// Calculate arc angle for each item
const getArc = () => (2 * Math.PI) / props.items.length;

// Main spin logic
const spin = () => {
  selected.value = null;
  const arc = getArc();

  const fullSpins = 5;
  const randomOffset = Math.random() * 2 * Math.PI;
  const targetAngle = angle.value + fullSpins * 2 * Math.PI + randomOffset;

  const duration = 5000;
  const startTime = performance.now();
  const startAngle = angle.value;

  const animate = (now: number) => {
    const elapsed = now - startTime;
    const progress = Math.min(elapsed / duration, 1);
    const easedProgress = 1 - Math.pow(1 - progress, 3);

    angle.value = startAngle + (targetAngle - startAngle) * easedProgress;
    drawWheel();

    if (progress < 1) {
      requestAnimationFrame(animate);
    } else {
      // Normalize the angle so 0 radians corresponds to the pointer direction (right)
      const finalAngle = angle.value % (2 * Math.PI);
      const adjustedAngle = (2 * Math.PI - finalAngle + 0) % (2 * Math.PI); // Pointer is at 0 rad

      const index = Math.floor(adjustedAngle / arc);
      selected.value = props.items[index];
      highlightSelectedItem(index);
    }
  };

  requestAnimationFrame(animate);
};

// Draw wheel segments
const drawWheel = () => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext("2d");
  if (!ctx) return;

  const { width, height } = canvas;
  const radius = width / 2;
  const arc = getArc();

  ctx.clearRect(0, 0, width, height);
  ctx.save();
  ctx.translate(radius, radius);
  ctx.rotate(angle.value);

  props.items.forEach((item, i) => {
    const startAngle = i * arc;

    ctx.beginPath();
    ctx.moveTo(0, 0);
    ctx.arc(0, 0, radius, startAngle, startAngle + arc);
    ctx.fillStyle = i % 2 === 0 ? "#4F46E5" : "#6366F1";
    ctx.fill();
    ctx.stroke();

    ctx.save();
    ctx.fillStyle = "#fff";
    ctx.rotate(startAngle + arc / 2);
    ctx.translate(radius * 0.65, 0);
    ctx.rotate(Math.PI / 2);
    ctx.textAlign = "center";
    ctx.font = "14px sans-serif";
    ctx.fillText(item, 0, 0);
    ctx.restore();
  });

  ctx.restore();
};

const highlightSelectedItem = (index: number) => {
  // Highlight the selected item (optional enhancement)
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext("2d");
  if (!ctx) return;

  const { width, height } = canvas;
  const radius = width / 2;
  const arc = (2 * Math.PI) / props.items.length;
  const startAngle = index * arc;

  ctx.clearRect(0, 0, width, height);
  ctx.save();
  ctx.translate(radius, radius);
  ctx.rotate(angle.value);

  props.items.forEach((item, i) => {
    const startAngle = i * arc;
    ctx.beginPath();
    ctx.moveTo(0, 0);
    ctx.arc(0, 0, radius, startAngle, startAngle + arc);
    ctx.fillStyle =
      i === index ? "#22C55E" : i % 2 === 0 ? "#4F46E5" : "#6366F1"; // Highlight selected item
    ctx.fill();
    ctx.stroke();

    // Text
    ctx.save();
    ctx.fillStyle = "#fff";
    ctx.rotate(startAngle + arc / 2);
    ctx.translate(radius * 0.65, 0);
    ctx.rotate(Math.PI / 2);
    ctx.textAlign = "center";
    ctx.font = "14px sans-serif";
    ctx.fillText(item, 0, 0);
    ctx.restore();
  });

  ctx.restore();
};

onMounted(drawWheel);
watch(() => props.items, drawWheel);
</script>

<style scoped>
canvas {
  cursor: pointer;
}
</style>
