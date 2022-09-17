<script setup>
import { ref, onMounted, watch } from "vue";
import GridComponent from "./components/GridComponent.vue";

const menuOptions = ["showAll", "filterByType"];

const isLoading = ref(true);
const results = ref([]);
const nextUrl = ref("");
const baseURL = "https://pokeapi.co/api/v2";
const pokemonTypes = ref([]);
const selectedPokemonTypeUrl = ref(null);
const currentMenu = ref(menuOptions[0]);

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

function isShowAll() {
  return currentMenu.value == menuOptions[0];
}

function isShowFilterByType() {
  return currentMenu.value == menuOptions[1];
}

function selectMenu(value) {
  currentMenu.value = value;
}
</script>

<template>
  <div class="flex flex-col text-center px-36 py-16 mb-8">
    <div class="text-4xl font-bold mb-16">Pokedex App ⚡️</div>
    <div class="flex flex-row-reverse mb-16">
      <div class="tabs tabs-boxed">
        <a
          class="tab"
          :class="{ 'tab-active': isShowAll() }"
          @click="selectMenu(menuOptions[0])"
        >
          Show All
        </a>
        <a
          class="tab"
          :class="{ 'tab-active': isShowFilterByType() }"
          @click="selectMenu(menuOptions[1])"
        >
          Filter by Type
        </a>
      </div>
    </div>
    <div v-if="!isLoading">
      <select
        class="select select-warning w-full max-w-xs mb-16"
        v-if="isShowFilterByType()"
        v-model="selectedPokemonTypeUrl"
      >
        <option disabled selected value="null">Filter by type..</option>
        <template v-for="(opt, index) in pokemonTypes" :key="index">
          <option :value="opt.url">
            {{ opt.name }}
          </option>
        </template>
      </select>
      <GridComponent :results="results" />
      <button
        class="btn btn-warning max-w-md mt-16"
        @click="fetchData"
        v-if="isShowAll()"
      >
        Load More
      </button>
    </div>
    <div v-else>
      <span class="text-4xl font-bold"> Loading..</span>
    </div>
  </div>
</template>
