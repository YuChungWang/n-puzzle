<template>
  <div class="container">
    <div
      :class="['wrapper', {
        three: base === 3,
        four: base === 4,
      }]"
    >
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

const base = 4;
let isValid = false;
const emptyValue = base * base;
let emptyIndex = 0;
let row = 0;
let col = 0;
const data = reactive(Array.from({length: base * base}, (_, index) => index + 1)); // [1, 2, 3, 4, 5, 6, 7, 8, 9]

const calcEmptyInfo = () => {
  emptyIndex = data.findIndex((id) => id === emptyValue);
  [row, col] = [Math.floor(emptyIndex / base), emptyIndex % base];
}
const checkIsValid = () => {
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

  if (
    (
      base % 2 === 1 &&
      inversionCount % 2 === 0
    ) ||
    (
      base % 2 === 0 &&
      (
        row % 2 === 0 && inversionCount % 2 === 1 ||
        row % 2 === 1 && inversionCount % 2 === 0
      )
    )
  ) {
    isValid = true;
  }
}
const init = () => {
  while (!isValid) {
    data.sort(() => Math.random() - 0.5);
    calcEmptyInfo();
    checkIsValid();
  }
};
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

    &.three {
      .box {
        &-9 {
          background: black;
        }
      }
    }
    &.four {
      grid-template: 1fr 1fr 1fr 1fr / 1fr 1fr 1fr 1fr;

      .box {
        width: 73px;
        height: 73px;

        &-16 {
          background: black;
        }
      }
    }

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
