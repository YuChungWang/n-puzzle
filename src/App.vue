<template>
  <div class="container">
    <div class="wrapper">
      <div v-for="id in data" :key="id" :class="['box', `box-${id}`]">{{ id === emptyValue ? '' : id }}</div>
    </div>
    <div class="controller">
      <button @click="handleBtnUpClick" class="btn top">↑</button>
      <div>
        <button @click="handleBtnLeftClick" class="btn left">←</button>
        <button @click="handleBtnDownClick" class="btn bottom">↓</button>
        <button @click="handleBtnRightClick" class="btn right">→</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive } from 'vue';

const base = 3;
const emptyValue = base * base;
let emptyIndex = 0;
let row = 0;
let col = 0;
const data = reactive(Array.from({length: base * base}, (_, index) => index + 1)); // [1, 2, 3, 4, 5, 6, 7, 8, 9]

const init = () => {
  let isValid = false;

  while (!isValid) {
    data.sort(() => Math.random() - 0.5);

    let inversionCount = 0;
    for (let i = 0; i < data.length - 1; i++) {
      if (data[i] === emptyValue) {
        continue;
      }
      for (let j = i + 1; j < data.length; j++) {
        if (data[j] === emptyValue) {
          continue;
        }
        if (data[j] < data[i]) {
          inversionCount += 1;
        }
      }
    }

    if (inversionCount % 2 === 0) {
      isValid = true;
    }
  }
};
const calcEmptyInfo = () => {
  emptyIndex = data.findIndex((id) => id === emptyValue);
  [row, col] = [Math.floor(emptyIndex / base), emptyIndex % base];
}
const switchPosition = (switchIndex = 0) => {
  [data[emptyIndex], data[switchIndex]] = [data[switchIndex], data[emptyIndex]];

  requestAnimationFrame(() => {
    // check finish
    let finish = true;
    for (let i = 0; i < data.length - 1; i++) {
      if (data[i] + 1 !== data[i + 1]) {
        finish = false;
        break;
      }
    }
    if (finish) {
      requestAnimationFrame(() => {
        alert('Congrats! you finished the puzzle')
      });
    }
  });

  // calc
  calcEmptyInfo();
}
const handleBtnUpClick = () => {
  if (row === base - 1) return;
  const switchIndex = (row + 1) * base + col;
  switchPosition(switchIndex);
}
const handleBtnDownClick = () => {
  if (row === 0) return;
  const switchIndex = (row - 1) * base + col;
  switchPosition(switchIndex);
}
const handleBtnLeftClick = () => {
  if (col === base - 1) return;
  const switchIndex = row * base + (col + 1);
  switchPosition(switchIndex);
}
const handleBtnRightClick = () => {
  if (col === 0) return;
  const switchIndex = row * base + (col - 1);
  switchPosition(switchIndex);
}

// event listener
window.addEventListener('keydown', (event) => {
  switch (event.key) {
    case 'ArrowUp':
      handleBtnUpClick();
      break;
    case 'ArrowDown':
      handleBtnDownClick();
      break;
    case 'ArrowLeft':
      handleBtnLeftClick();
      break;
    case 'ArrowRight':
      handleBtnRightClick();
      break;
    default:
      break;
  }
});

init();
calcEmptyInfo();
</script>

<style lang="scss" scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 500px;

  .wrapper {
    display: grid;
    grid-template: 1fr 1fr 1fr / 1fr 1fr 1fr;
    width: 300px;
    height: 300px;
    border: 4px solid #fcfcfc;
    border-radius: 6px;

    .box {
      display: flex;
      justify-content: center;
      align-items: center;
      width: 98px;
      height: 98px;
      background: darkolivegreen;
      border: 1px solid #fcfcfc;
      font-size: 20px;
      transition: 0.3s;
  
      &-9 {
        background: black;
      }
    }
  }

  .controller {
    width: 300px;
    height: 100px;
    margin-top: 40px;

    .btn {
      width: 40px;
      height: 40px;
      margin: 2px;
      padding: 0;
      border: 1px solid #fff;
    }
  }
}
</style>
