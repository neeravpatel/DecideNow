<template>
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-100 text-gray-800">
    <!-- Random Box Grid -->
    <div class="grid grid-cols-3 gap-4 max-w-md mt-6">
      <!-- Random Boxes -->
      <div
        v-for="(item, index) in items"
        :key="index"
        class="box p-4 bg-blue-500 rounded-lg text-white flex justify-center items-center"
        :class="{'zoom-in': selected === item}"
        :style="getBoxStyle(index)"
      >
        {{ item }}
      </div>
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
import { ref } from 'vue';

// Props passed into the component (array of items)
defineProps({
  items: {
    type: Array,
    required: true,
  },
});

const selected = ref<string | null>(null);
const shuffle = ref(false);

// Style calculation for the boxes (shuffling effect)
const getBoxStyle = (index: number) => {
  const offset = Math.random() * 100; // Randomize Y-axis position for the shuffle
  return {
    transform: shuffle.value ? `translateY(${offset}px)` : 'translateY(0)',
    transition: shuffle.value ? 'transform 1s ease-out' : 'transform 0.3s ease-in',
  };
};

// Function to start the shuffle and reveal process
const spin = () => {
  shuffle.value = true;

  // Simulate a shuffle animation for 1 second before revealing the selection
  setTimeout(() => {
    const randomIndex = Math.floor(Math.random() * props.items.length); // Random index
    selected.value = props.items[randomIndex]; // Set the selected item
    shuffle.value = false; // Reset shuffle effect
  }, 1000); // Shuffle animation lasts for 1 second
};
</script>

<style scoped>
/* Box styles */
.box {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100px;
  font-size: 16px;
  text-align: center;
  cursor: pointer;
}

.zoom-in {
  animation: zoomIn 0.3s ease-out forwards;
}

@keyframes zoomIn {
  0% {
    transform: scale(0.8);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
