<script setup>
import Question from "./components/Question.vue";
</script>

<template>
  <div>
    <nav class="flex items-center justify-between p-6 bg-white shadow-md">
      <a href=""><img src="/logo.jpeg" alt="Logo" class="h-16" /></a>
      <h1 class="text-center text-4xl text-gray-600 font-semibold">Chiikawa Quiz</h1>
    </nav>
    <div class="flex flex-col items-center justify-center p-12">
      <div class="w-full max-w-2xl p-6 bg-white rounded-lg shadow-md">
        <Question />
      </div>
    </div>
    <footer class="text-center mt-24 flex flex-col items-center justify-center p-6 bg-gray-100 rounded-lg shadow-md">
      <h2 class="text-gray-600">Feito por Mário Dias em Vue.js e TailwindCSS</h2>
      <h2 class="text-gray-600">2025</h2>
    </footer>
  </div>
</template>