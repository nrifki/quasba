<script setup lang="ts">
import { onMounted, ref } from "vue";

const randomQuote = ref({});

onMounted(async () => {
  const response = await fetch("/data/quotes.json");
  const quotes = await response.json();

  randomQuote.value = quotes[Math.floor(Math.random() * quotes.length)];
});
</script>

<template>
  <div>{{ randomQuote }}</div>
</template>
