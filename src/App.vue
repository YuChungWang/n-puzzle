<template>
  <div class="container">
    <div v-if="!isGaming" class="init">
      <p class="title">Puzzle Game</p>
      <div class="level-options">
        <button class="btn-level" @click="initGame('EASY')">Easy</button>
        <button class="btn-level" @click="initGame('HARD')">Hard</button>
      </div>
    </div>
    <div v-else class="game">
      <p class="time">{{ sec }}</p>
      <button class="btn-return" @click="handleBtnReturnClick">←</button>
      <div
        :class="['puzzle', {
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
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue';

// configs
let base = 3;

// custom variables
let emptyValue = base * base;
let data = []; // [1, 2, 3, 4, 5, 6, 7, 8, 9]
let isGaming = ref(false);
let isValid = false;
let timer = null;
let sec = ref(0);
let emptyIndex = 0;
let emptyRow = 0;
let emptyCol = 0;

const calcEmptyInfo = () => {
  emptyIndex = data.findIndex((id) => id === emptyValue);
  [emptyRow, emptyCol] = [Math.floor(emptyIndex / base), emptyIndex % base];
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
        emptyRow % 2 === 0 && inversionCount % 2 === 1 ||
        emptyRow % 2 === 1 && inversionCount % 2 === 0
      )
    )
  ) {
    isValid = true;
  }
}
const initGame = (level) => {
  base = level === 'EASY' ? 3 : 4;
  emptyValue = base * base;
  data = reactive(Array.from({length: base * base}, (_, index) => index + 1));

  while (!isValid) {
    data.sort(() => Math.random() - 0.5);
    calcEmptyInfo();
    checkIsValid();
  }

  isGaming.value = true;
  timer = setInterval(() => {
    sec.value += 1;
  }, 1000);
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
      clearInterval(timer);
      requestAnimationFrame(() => {
        alert(`Congrats! you finished the puzzle in ${sec.value}s`)
      });
      isGaming.value = false;
      sec.value = 0;
      isValid = false;
    }
  });

  // calc
  calcEmptyInfo();
}
const handleBtnReturnClick = () => {
  clearInterval(timer);
  isGaming.value = false;
  sec.value = 0;
  isValid = false;
}
const handleBtnUpClick = () => {
  if (emptyRow === base - 1) return;
  const switchIndex = (emptyRow + 1) * base + emptyCol;
  switchPosition(switchIndex);
}
const handleBtnDownClick = () => {
  if (emptyRow === 0) return;
  const switchIndex = (emptyRow - 1) * base + emptyCol;
  switchPosition(switchIndex);
}
const handleBtnLeftClick = () => {
  if (emptyCol === base - 1) return;
  const switchIndex = emptyRow * base + (emptyCol + 1);
  switchPosition(switchIndex);
}
const handleBtnRightClick = () => {
  if (emptyCol === 0) return;
  const switchIndex = emptyRow * base + (emptyCol - 1);
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
</script>

<style lang="scss" scoped>
.container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 500px;
  height: 500px;

  .init {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 480px;

    .title {
      font-size: 48px;
    }

    .level-options {
      .btn-level {
        margin: 0 10px;
      }
    }
  }

  .game {
    .time {
      position: fixed;
      top: 20px;
      right: 20px;
      margin: 0;
    }

    .btn-return {
      position: fixed;
      top: 20px;
      left: 20px;
      // height: 24px;
    }

    .puzzle {
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
}
</style>
