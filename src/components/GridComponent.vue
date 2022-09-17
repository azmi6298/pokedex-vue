<template>
  <div class="grid xl:grid-cols-4 gap-8">
    <div v-for="(result, index) in results" :key="index">
      <CardComponent :name="result.name" :url="result.url" />
    </div>
  </div>
</template>

<script setup>
import CardComponent from "./CardComponent.vue";
defineProps({
  results: Array,
});
</script>
