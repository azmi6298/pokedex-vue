<template>
  <div class="card max-w-96 bg-base-100 shadow-xl">
    <div class="card-body flex">
      <span>{{ name }}</span>
      <span>{{ url }}</span>
    </div>
    <label
      for="my-modal-5"
      class="btn btn-warning modal-button"
      @click="handleModal"
    >
      See More
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
