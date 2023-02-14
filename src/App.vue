<template>
  <div class="container">
    <div v-for="(id, idx) in data" :key="idx" :class="['box', `box-${idx}`]">{{ id }}</div>
  </div>
  <button @click="handleSwitch" class="btn-switch">Switch</button>
</template>

<script setup>
import { computed } from 'vue';

const data = computed(() => Array.from({length: 9}, (_, index) => index + 1)); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
data.value.sort(() => Math.random() - 0.5);

const handleSwitch = () => {
  const temp = data.value[0];
  data.value[0] = data.value[1];
  data.value[1] = temp;
}
</script>

<style lang="scss" scoped>
.container {
  display: grid;
  grid-template: 1fr 1fr 1fr / 1fr 1fr 1fr;
  width: 600px;
  height: 600px;
  border: 1px solid #fcfcfc;
  border-radius: 6px;

  .box {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 198px;
    height: 198px;
    border: 1px solid #fcfcfc;
  }
}

.btn-switch {
  margin-top: 20px;
}
</style>
