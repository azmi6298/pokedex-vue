<template>
  <div class="card w-96 bg-base-100 shadow-xl">
    <div class="card-body">
      <span>{{ name }}</span>
      <span>{{ url }}</span>
    </div>
  </div>
</template>

<script setup>
defineProps({
  name: String,
  url: String,
});
</script>
