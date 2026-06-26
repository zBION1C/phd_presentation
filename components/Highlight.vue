<script setup>
defineProps({
  color: {
    type: String,
    default: "#ffb3ba"
  },
  icon: {
    type: String,
    default: "/public/images/exclamation.svg"
  },

})
</script>

<template>
  <div class="flex flex-row warning-box">
    <img :src="icon" width=50px/>
    <div class="definition-content">
      <slot />
    </div>
  </div>
</template>

<style scoped>
.warning-box {
  background: v-bind(color);
  border-radius: 5px;
  padding: 5px;
  border: 1px black solid;
}

.definition-content {
  margin-left: 1rem;
}

</style>
