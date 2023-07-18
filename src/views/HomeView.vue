<script setup lang="ts">
import { onMounted, ref, nextTick } from "vue";

const randomQuote = ref<{
  quote: string;
  author: string;
  book: string;
  chapter: string;
  page: string;
}>({ quote: "", author: "", book: "", chapter: "", page: "" });

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
    const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
    gradient.addColorStop(0, "black");
    gradient.addColorStop(0.6, "gray");

    ctx.fillStyle = gradient;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "white";
    ctx.font = "48px Arial";
    ctx.textAlign = "center";

    const y = canvas.height / 2; // Adjust to center text vertically
    wrapText(
      ctx,
      randomQuote.value.quote,
      canvas.width / 2,
      y,
      canvas.width - 50,
      48
    ); // Leave 50px padding on either side
  }
  generateImage();
};

const wrapText = (context, text, x, y, maxWidth, lineHeight) => {
  const words = text.split(" ");
  let line = "";

  for (let n = 0; n < words.length; n++) {
    const testLine = line + words[n] + " ";
    const metrics = context.measureText(testLine);
    const testWidth = metrics.width;
    if (testWidth > maxWidth && n > 0) {
      context.fillText(line, x, y);
      line = words[n] + " ";
      y += lineHeight;
    } else {
      line = testLine;
    }
  }
  context.fillText(line, x, y);
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
  <div class="w-full">
    <a id="downloadLink">Download Quote</a>
    <canvas id="quoteCanvas" height="1792" width="828"></canvas>
  </div>
</template>
