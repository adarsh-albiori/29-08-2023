<template>
  <v-card class="mt-16 mx-auto" elevation="10" width="850px">
    <p class="text-center my-2 text-h4" :style="titleStyles">Bingo Board</p>
    <div v-if="showRandomNumber" class="text-center my-2 text-h4">
      {{ bingo }}
    </div>
    <div v-if="showWinner" class="text-center my-2 text-h4">
      <p>Congratulations!! You are winner</p>
    </div>
    <v-card color="grey-lighten-4" class="mx-5 px-5 d-flex justify-center" flat>
      <div class="wrapper">
        <div
          class="box"
          v-for="(number, index) in bingoBoardNumbers"
          :key="index"
          :style="getColumnStyle(number, index)"
        >
          {{ number }}
        </div>
      </div>
    </v-card>
    <v-card-actions class="d-flex justify-center my-2">
      <v-btn
        color="deep-purple"
        variant="elevated"
        class="text-sm"
        :disabled="!disableStartBtn"
        @click="start"
        >Start</v-btn
      >
      <v-btn
        color="deep-purple"
        variant="elevated"
        class="text-sm"
        @click="reset"
        >Refresh</v-btn
      >
    </v-card-actions>
  </v-card>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const bingoBoardNumbers = ref([]);
const bingo = ref(null);
const showRandomNumber = ref(false);
const showWinner = ref(false);
let startBingo = ref(null);
const disableStartBtn = ref(true);
const includedColumnIndexes = ref([]);

const TIME_MULTIPLYER = 0.2;

const currentTime = ref(Date.now());

const titleStyles = computed(() => {
  // Logic for RGB Title Name

  const r = Math.round(
    Math.abs(Math.sin(currentTime.value * TIME_MULTIPLYER)) * 255
  );
  const g = Math.round(
    Math.abs(
      Math.sin(currentTime.value * TIME_MULTIPLYER + (2 * Math.PI) / 3)
    ) * 255
  );
  const b = Math.round(
    Math.abs(
      Math.sin(currentTime.value * TIME_MULTIPLYER + (4 * Math.PI) / 3)
    ) * 255
  );

  return {
    color: `rgb(${r}, ${g}, ${b})`,
  };
});
setInterval(() => {
  currentTime.value = Date.now();
}, 200);

function start() {
  // Logic for generating random numbers
  disableStartBtn.value = false;
  showRandomNumber.value = true;
  startBingo.value = setInterval(() => {
    bingo.value = Math.floor(Math.random() * 99) + 1;
  }, 300);
}

watch(bingo, (newBingo) => {
  if (
    bingoBoardNumbers.value.includes(newBingo) &&
    !includedColumnIndexes.value.includes(newBingo)
  ) {
    includedColumnIndexes.value.push(newBingo);
  }
});

watch(
  () => includedColumnIndexes.value.length,
  (newLength) => {
    if (newLength === 25) {
      showRandomNumber.value = false;
      showWinner.value = true;
      clearInterval(startBingo.value);
    }
  }
);
function reset() {
  disableStartBtn.value = true;
  // logic for resetting the bingo board
  const newNumbers = [];
  while (newNumbers.length < 25) {
    const randomNumber = Math.floor(Math.random() * 99) + 1;
    if (!newNumbers.includes(randomNumber)) {
      newNumbers.push(randomNumber);
    }
  }
  clearInterval(startBingo.value);
  // clearing the set timeout interval
  showRandomNumber.value = false;
  bingoBoardNumbers.value = newNumbers;
  includedColumnIndexes.value = [];
}
reset();

function getColumnStyle(index) {
  return {
    // logic for changing color of colunms to green if matched with generated randomNumber
    backgroundColor: includedColumnIndexes.value.includes(index)
      ? "green"
      : "transparent",
    color: includedColumnIndexes.value.includes(index) ? "white" : "black",
  };
}
</script>

<style scoped>
:root {
  --box-color: black;
}

.box {
  color: var(--box-color);
  padding-top: 15px;
  text-align: center;
  font-size: 100%;
}

.wrapper {
  display: grid;
  grid-template-columns: repeat(5, 165px);
  grid-template-rows: repeat(5, 55px);
  grid-auto-flow: column;
}
</style>
