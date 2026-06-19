<script setup>
defineProps({
  title: {
    type: String,
    default: "Title",
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
  <div class="definition-box">
    <div class="definition-title">{{ title }}</div>
    <div class="definition-content">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.definition-box {
  background: v-bind(color);
  border-radius: 5px;
  padding: 5px;
  width: v-bind(width);
  height: v-bind(height);
  border: 2px black solid;
}

.definition-title {
  font-weight: 600;
  text-align: center;
}

.definition-content {
  margin-left: 1rem;
}

</style>
