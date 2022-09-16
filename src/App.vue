<script setup>
import { ref, onMounted, watch } from "vue";
import CardComponent from "./components/CardComponent.vue";

const isLoading = ref(true);
const results = ref([]);
const nextUrl = ref("");
const baseURL = "https://pokeapi.co/api/v2";
const pokemonTypes = ref([]);
const selectedPokemonTypeUrl = ref(null);

// fetch pokemons from pokedex api
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
  return true;
}

// fetch pokemon type list
function fetchTypes() {
  fetch(`${baseURL}/type`)
    .then((res) => res.json())
    .then((data) => {
      pokemonTypes.value = [...data.results];
    });
  return true;
}

// fetch pokemon by type
function fetchByType() {
  fetch(`${selectedPokemonTypeUrl.value}`)
    .then((res) => res.json())
    .then((data) => {
      const pokemonByType = data.pokemon.map((poke) => poke.pokemon);
      results.value = [...pokemonByType];

      isLoading.value = false;
    });
}

// fetch data on mounted
onMounted(() => {
  fetchTypes();
  fetchData();
  if (fetchData() && fetchTypes()) {
    isLoading.value = false;
  }
});

// watch pokemon type
watch(selectedPokemonTypeUrl, () => {
  isLoading.value = true;
  fetchByType();
});
</script>

<template>
  <div class="flex flex-col text-center px-36 py-16 mb-8">
    <div class="text-4xl font-bold mb-16">Pokedex App ⚡️</div>
    <div v-if="!isLoading">
      <select
        class="select select-warning w-full max-w-xs mb-16"
        v-model="selectedPokemonTypeUrl"
      >
        <option disabled selected value="null">Filter by type..</option>
        <template v-for="(opt, index) in pokemonTypes" :key="index">
          <option :value="opt.url">
            {{ opt.name }}
          </option>
        </template>
      </select>
      <div class="grid grid-cols-4 gap-8">
        <div v-for="(result, index) in results" :key="index">
          <CardComponent :name="result.name" :url="result.url" />
        </div>
      </div>
      <button class="btn btn-warning max-w-md mt-16" @click="fetchData">
        Load More
      </button>
    </div>
    <div v-else>
      <span class="text-4xl font-bold"> Loading..</span>
    </div>
  </div>
</template>
