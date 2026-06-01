<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

const images = ref<string[]>([]);
const currentPage = ref(0);

function handleFiles(event: Event) {
  const input = event.target as HTMLInputElement;

  if (!input.files) return;

  images.value.forEach((url) => URL.revokeObjectURL(url));
  images.value = [];

  const files = Array.from(input.files);

  files.sort((a, b) => a.name.localeCompare(b.name));

  files.forEach((file) => {
    images.value.push(URL.createObjectURL(file));
  });

  currentPage.value = 0;
}

function nextPage() {
  if (currentPage.value < images.value.length - 1) {
    currentPage.value++;
  }
}

function previousPage() {
  if (currentPage.value > 0) {
    currentPage.value--;
  }
}

function handleKeydown(event: KeyboardEvent) {
  if (event.key === "ArrowRight") {
    nextPage();
  }

  if (event.key === "ArrowLeft") {
    previousPage();
  }

  if (event.code === "Space") {
    event.preventDefault();

    if (currentPage.value < images.value.length - 1) {
      nextPage();
    } else {
      currentPage.value = 0;
    }
  }
}


onMounted(() => {
  window.addEventListener("keydown", handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
});

</script>

<template>
  <div class="container">
    <h1>Yomu</h1>

    <input
      type="file"
      multiple
      accept="image/*"
      @change="handleFiles"
    />

    <div v-if="images.length > 0">
      <p>
        Page {{ currentPage + 1 }} / {{ images.length }}
      </p>

      <img
        :src="images[currentPage]"
        class="manga-page"
      />

      <div class="buttons">
        <button @click="previousPage">
          Previous
        </button>

        <button @click="nextPage">
          Next
        </button>
      </div>
    </div>
  </div>
</template>






<style>
body {
  margin: 0;
  background: #111;
  color: white;
  font-family: sans-serif;
}

.container {
  text-align: center;
  padding: 2rem;
}

.manga-page {
  max-width: 90%;
  max-height: 80vh;
  margin-top: 1rem;
}

.buttons {
  margin-top: 1rem;
}

button {
  margin: 0 0.5rem;
  padding: 0.5rem 1rem;
}
</style>