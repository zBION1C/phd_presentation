<script setup>
defineProps({
  content: {
    type: String,
    default: "Content",
  },
  color: {
    type: String,
    default: "red"
  },
  width: {
    type: String,
    default: "150px"
  },
  height: {
    type: String,
    default: "50px"
  },
})
</script>

<template>
  <div class="card">
    <div class="card-content">{{ content }}</div>
  </div>
</template>

<style scoped>
.card {
  display: flex;
  justify-content: center;
  align-items: center;
  background: v-bind(color);
  border-radius: 5px;
  padding: 5px;
  width: v-bind(width);
  height: v-bind(height);
  border: 2px black solid;
}

</style>
