<template>
  <v-card class="mt-16 mx-auto" elevation="10" width="850px">
    <p class="text-center my-2 text-h4" :style="titleStyles">Bingo Board</p>
    <div v-if="show" class="text-center my-2 text-h4">
      {{ bingo }}
    </div>
    <v-card color="grey-lighten-4" class="mx-5 px-5 py-2 d-flex justify-center">
      <div class="wrapper">
        <div
          class="box"
          v-for="(number, index) in bingoNumbers"
          :key="index"
          :style="{
            backgroundColor: isBingo ? 'green' : 'transparent',
          }"
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
import { ref, computed } from "vue";

const bingoNumbers = ref([]);
const bingo = ref(null);
const show = ref(false);
let startBingo = ref(null);
const isBingo = ref(null);

const timeMultiplier = 0.2;

const currentTime = ref(Date.now());

const titleStyles = computed(() => {
  const r = Math.round(
    Math.abs(Math.sin(currentTime.value * timeMultiplier)) * 255
  );
  const g = Math.round(
    Math.abs(Math.sin(currentTime.value * timeMultiplier + (2 * Math.PI) / 3)) *
      255
  );
  const b = Math.round(
    Math.abs(Math.sin(currentTime.value * timeMultiplier + (4 * Math.PI) / 3)) *
      255
  );

  return {
    color: `rgb(${r}, ${g}, ${b})`,
  };
});
setInterval(() => {
  currentTime.value = Date.now();
}, 200);

function start() {
  show.value = true;
  startBingo.value = setInterval(() => {
    bingo.value = ref(Math.floor(Math.random() * 99) + 1);
    if (bingoNumbers.value.includes(bingo.value._value)) {
      isBingo.value = true;
    }
  }, 300);
}

function reset() {
  const newNumbers = [];
  while (newNumbers.length < 25) {
    const randomNumber = Math.floor(Math.random() * 99) + 1;
    if (!newNumbers.includes(randomNumber)) {
      newNumbers.push(randomNumber);
    }
  }
  clearInterval(startBingo.value);
  show.value = false;
  bingoNumbers.value = newNumbers;
}
reset();
</script>

<style scoped>
.box {
  /* background-color: lightgrey; */
  color: #000000;
  border-radius: 5px;
  padding-top: 15px;
  text-align: center;
  font-size: 100%;
}
.wrapper {
  display: grid;
  grid-gap: 8px;
  grid-template-columns: repeat(5, 155px);
  grid-template-rows: repeat(5, 50px);
  grid-auto-flow: column;
}
/* Add your custom styling here */
</style>
