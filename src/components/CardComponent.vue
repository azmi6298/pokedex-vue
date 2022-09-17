<template>
  <div class="card max-w-md bg-base-100 shadow-xl">
    <div class="card-body flex">
      <p class="capitalize text-2xl font-bold">{{ name }}</p>
    </div>
    <label
      for="my-modal-5"
      class="btn btn-warning modal-button"
      @click="handleModal"
    >
      See Details
    </label>

    <ModalComponent
      v-if="isModalOpen"
      :pokemon-name="name"
      :pokemon-url="url"
      @close="handleModal"
    />
  </div>
</template>

<script setup>
import ModalComponent from "./ModalComponent.vue";
import { ref } from "vue";
defineProps({
  name: String,
  url: String,
});

const isModalOpen = ref(false);

function handleModal() {
  isModalOpen.value = !isModalOpen.value;
}
</script>
