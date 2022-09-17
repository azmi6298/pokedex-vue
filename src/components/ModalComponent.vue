<template>
  <div>
    <!-- Put this part before </body> tag -->
    <input type="checkbox" id="my-modal-5" class="modal-toggle" />
    <div class="modal">
      <div class="modal-box w-11/12 max-w-5xl">
        <label
          @click="handleCloseModal"
          for="my-modal-5"
          class="btn btn-sm btn-circle absolute right-2 top-2"
          >✕</label
        >
        <p class="font-bold text-2xl uppercase">{{ props.pokemonName }}</p>
        <div v-if="isLoading">Loading Pokemon Data...</div>
        <div v-else-if="!isLoading && pokemonData != null">
          <div>{{ pokemonData.types }}</div>
          <div class="grid grid-cols-8">
            <template v-for="(sprite, idx) in pokemonData.sprites" :key="idx">
              <img :src="sprite" class="object-contain h-36" />
            </template>
          </div>
          <div class="mb-8">
            <p class="text-xl font-semibold">Ability</p>
            <template
              v-for="(ability, idx) in pokemonData.abilities"
              :key="idx"
            >
              <div>{{ ability }}</div>
            </template>
          </div>
          <div>
            <p class="text-xl font-semibold">Moves</p>
            <template v-for="(move, idx) in pokemonData.moves" :key="idx">
              <div>{{ move }}</div>
            </template>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

const props = defineProps({
  pokemonName: String,
  pokemonUrl: String,
});

const url = ref("");
const pokemonData = ref(null);
const isLoading = ref(true);

onMounted(() => {
  url.value = props.pokemonUrl;
  fetchPokemon();
});

function fetchPokemon() {
  fetch(`${url.value}`)
    .then((res) => res.json())
    .then((data) => {
      const abilities = data.abilities.map((ability) => ability.ability.name);
      const types = data.types.map((type) => type.type.name);
      const moves = data.moves.map((move) => move.move.name);
      const versions = Object.values(data.sprites.versions);
      let sprites = versions.map((version) => Object.values(version));
      sprites = sprites.map((sprite) => sprite[0].front_default);

      let filteredData = {
        abilities,
        types: types.join(", "),
        moves,
        sprites,
      };

      pokemonData.value = filteredData;
      isLoading.value = false;
    });
}

// emit to close modal
const emit = defineEmits(["close"]);
function handleCloseModal() {
  emit("close");
}
</script>
