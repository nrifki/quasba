<script setup lang="ts">
import { onMounted, ref, nextTick } from "vue";

const randomQuote = ref<{
  quote: string;
  author: string;
  book: string;
  chapter: string;
  page: string;
}>({
  quote: "",
  author: "",
  book: "",
  chapter: "",
  page: "",
});

onMounted(async () => {
  const response = await fetch("/data/quotes.json");
  const quotes = await response.json();

  randomQuote.value = quotes[Math.floor(Math.random() * quotes.length)];
  await nextTick();
  drawQuote();
});

const drawQuote = () => {
  const canvas = document.getElementById("quoteCanvas") as HTMLCanvasElement;
  const ctx = canvas.getContext("2d");

  if (ctx) {
    ctx.fillStyle = "white";
    ctx.font = "20px Arial";
    ctx.textAlign = "center";
    ctx.fillText(randomQuote.value.quote, canvas.width / 2, canvas.height / 2);
  }
  generateImage();
};

const generateImage = () => {
  const canvas = document.getElementById("quoteCanvas") as HTMLCanvasElement;
  const downloadLink = document.getElementById(
    "downloadLink"
  ) as HTMLAnchorElement;

  downloadLink.href = canvas.toDataURL("image/png");
  downloadLink.download = "quote.png";
};
</script>

<template>
  <div>
    <canvas id="quoteCanvas" width="500" height="500"></canvas>
    <a id="downloadLink">Download Quote</a>
  </div>
</template>
