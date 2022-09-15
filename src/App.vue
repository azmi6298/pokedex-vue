<script setup>
import { ref, onMounted } from "vue";
import CardComponent from "./components/CardComponent.vue";

const isLoading = ref(true);
const results = ref([]);
const nextUrl = ref("");
const baseURL = "https://pokeapi.co/api/v2";

// fetch from pokedex api
function fetchData() {
  if (nextUrl.value == "") {
    fetch(`${baseURL}/pokemon/?limit=12`)
      .then((res) => res.json())
      .then((data) => {
        nextUrl.value = data.next;
        results.value = [...data.results];
      });
  } else {
    fetch(`${nextUrl.value}`)
      .then((res) => res.json())
      .then((data) => {
        nextUrl.value = data.next;
        results.value = [...results.value, ...data.results];
      });
  }
}

// fetch data on mounted
onMounted(() => {
  fetchData();
  isLoading.value = false;
});
</script>

<template>
  <div class="flex flex-col text-center px-36 py-16 mb-8">
    <div class="text-4xl">Pokedex App ⚡️</div>
    <div v-if="!isLoading">
      <div class="grid grid-cols-4 gap-y-8">
        <div v-for="(result, index) in results" :key="index">
          <CardComponent :name="result.name" :url="result.url" />
        </div>
      </div>
      <button class="btn btn-primary max-w-md mt-8" @click="fetchData">
        Load More
      </button>
    </div>
    <div v-else>
      <span class="text-4xl font-bold"> Loading..</span>
    </div>
  </div>
</template>
