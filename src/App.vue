<template>
  <div class="min-h-screen flex flex-col bg-gray-100 text-gray-800">
    <!-- Header -->
    <header class="bg-white shadow-md py-4 px-6 sticky top-0 z-20">
      <div class="max-w-3xl mx-auto flex justify-between items-center">
        <h1 class="text-2xl font-bold text-blue-600">🎯 DecideNow</h1>
        <span class="text-sm text-gray-500">Make choices the fun way</span>
      </div>
    </header>

    <!-- Main Content -->
    <main class="flex-grow flex items-center justify-center p-4">
      <WheelSpinner :items="['Pizza', 'Sushi', 'Burger', 'Tacos', 'Salad']" />
    </main>

    <!-- Footer -->
    <footer class="bg-white shadow-inner py-3 px-6">
      <div class="max-w-3xl mx-auto text-center text-sm text-gray-500">
        Developed by Neerav Patel | Powered by Netlify
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import WheelSpinner from './components/WheelSpinner.vue'
</script>
