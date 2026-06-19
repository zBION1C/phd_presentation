<script setup>
defineProps({
  title: {
    type: String,
    default: "Title",
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
  <div class="flex flex-row warning-box">
    <img src="/images/exclamation.svg" width=50px/>
    <div class="definition-content">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.warning-box {
  background: #ffb3ba;
  border-radius: 5px;
  padding: 5px;
  border: 2px black solid;
}

.definition-content {
  margin-left: 1rem;
}

</style>
