<script setup>
import { reactive, ref } from "vue";
import file1 from "./assets/1.mp3";
import file2 from "./assets/2.mp3";
import file3 from "./assets/3.mp3";
import file4 from "./assets/4.mp3";
import MyRadioButton from "./components/MyRadioButton.vue";

const audioObject = {
  1: file1,
  2: file2,
  3: file3,
  4: file4,
};

const isStart = ref(false);
const difficultyLevel = ref(1.5);
const currentBlock = ref(0);
const roundNumber = reactive({ round: 1 });
const array = ref([]);
const yourTurn = ref([]);
const isPlay = ref(false);
const clickNumber = ref(0);
const isFailure = ref(false);

function audioPlay() {
  const audio = new Audio(audioObject[currentBlock.value]);
  audio.volume = 0.2;
  audio.play();
}

function playRound() {
  isPlay.value = true;
  const newNum = Math.floor(Math.random() * 4) + 1;
  array.value.push(newNum);

  currentBlock.value = array.value[0];
  audioPlay();

  let i = 1;
  const timer = setInterval(() => {
    currentBlock.value = array.value[i];
    audioPlay();

    if (i === array.value.length) {
      isPlay.value = false;
      clearInterval(timer);
      currentBlock.value = 0;
    }

    i++;
  }, difficultyLevel.value * 1000);
}

function reset() {
  isStart.value = false;
  currentBlock.value = 0;
  difficultyLevel.value = 1.5;
  array.value = [];
  yourTurn.value = [];
  isPlay.value = false;
  clickNumber.value = 0;
  isFailure.value = true;
}

function delay(ms) {
  return new Promise((resolve) => setTimeout(() => resolve(), ms));
}

async function click(id) {
  isPlay.value = true;
  currentBlock.value = 0;
  await delay(0);
  currentBlock.value = id;
  audioPlay();
  yourTurn.value.push(id);

  // проигрыш
  if (array.value[clickNumber.value] !== yourTurn.value[clickNumber.value]) {
    reset();
    return;
  }

  clickNumber.value = clickNumber.value + 1;

  // след. раунд
  if (yourTurn.value.length === array.value.length) {
    const timer = setTimeout(() => {
      roundNumber.round++;
      yourTurn.value = [];
      clickNumber.value = 0;
      currentBlock.value = 0;
      clearTimeout(timer);
      playRound();
    }, difficultyLevel.value * 1000);
  }
  isPlay.value = false;
}
</script>

<template>
  <div
    v-if="isFailure"
    class="failModal"
    @click="
      () => {
        isFailure = false;
        roundNumber.round = 1;
      }
    "
  >
    <div class="modalContent">
      <p>Вы проиграли!</p>
    </div>
  </div>

  <div class="gameContainer">
    <h1 class="title">Simon The Game</h1>
    <button
      v-if="!isStart"
      class="startButton"
      @click="
        () => {
          isStart = true;
          playRound();
        }
      "
      :disabled="isStart"
    >
      Старт
    </button>
    <section v-if="!isStart" class="difficultContainer">
      <h1>Выберите уровень сложности</h1>
      <div class="difficultWrapper">
        <div class="difficult">
          <MyRadioButton
            checked
            name="lvl"
            id="easy"
            :value="1.5"
            v-model="difficultyLevel"
          />
          <label for="easy">Легкий</label>
        </div>
        <div class="difficult">
          <MyRadioButton
            name="lvl"
            id="medium"
            :value="1"
            v-model="difficultyLevel"
          />
          <label for="medium">Средний</label>
        </div>
        <div class="difficult">
          <MyRadioButton
            name="lvl"
            id="hard"
            :value="0.4"
            v-model="difficultyLevel"
          />
          <label for="hard">Сложный</label>
        </div>
      </div>
    </section>

    <p v-if="isStart" class="round">Round: {{ roundNumber.round }}</p>
    <div v-if="isStart" class="game">
      <button
        class="gameButton yellow"
        :class="{ active: currentBlock === 1 }"
        :style="{
          animationDuration: `${difficultyLevel}s`,
          animationIterationCount: isPlay ? `infinite` : `1`,
        }"
        :disabled="!isStart || isPlay"
        @click="
          () => {
            click(1);
          }
        "
      />
      <button
        class="gameButton blue"
        :class="{ active: currentBlock === 2 }"
        :style="{
          animationDuration: `${difficultyLevel}s`,
          animationIterationCount: isPlay ? `infinite` : `1`,
        }"
        :disabled="!isStart || isPlay"
        @click="
          () => {
            click(2);
          }
        "
      />
      <button
        class="gameButton red"
        :class="{ active: currentBlock === 3 }"
        :style="{
          animationDuration: `${difficultyLevel}s`,
          animationIterationCount: isPlay ? `infinite` : `1`,
        }"
        :disabled="!isStart || isPlay"
        @click="
          (e) => {
            click(3);
          }
        "
      />
      <button
        class="gameButton green"
        :class="{ active: currentBlock === 4 }"
        :style="{
          animationDuration: `${difficultyLevel}s`,
          animationIterationCount: isPlay ? `infinite` : `1`,
        }"
        :disabled="!isStart || isPlay"
        @click="
          () => {
            click(4);
          }
        "
      />
    </div>
  </div>
</template>

<style scoped>
.failModal {
  position: fixed;
  inset: 0;
  z-index: 999;
  background-color: rgba(0, 0, 0, 0.504);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modalContent {
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 300px;
  min-height: 100px;
  padding: 10px;
  border-radius: 20px;
  font-size: 2rem;
  color: white;
  background-color: rgb(135, 0, 0);
}
.title {
  font-size: 3rem;
  font-weight: bolder;
  background: linear-gradient(
    120deg,
    yellow 20%,
    blue 25% 45%,
    red 50% 70%,
    green 75% 95%
  );

  color: transparent;
  background-clip: text;
  -webkit-background-clip: text;
}
.startButton {
  padding: 20px;
  font-size: 2rem;
  border-radius: 20px;
  color: var(--secondary);
  background-color: var(--primary);
  border: 2px solid var(--secondary);
  transition: 0.2s linear;
}
.startButton:hover {
  background-color: var(--secondary);
  color: var(--primary);
}
.difficultContainer {
  display: flex;
  flex-direction: column;
  gap: 20px;
}
.difficultWrapper {
  display: flex;
  justify-content: space-around;
}
.difficult {
  display: flex;
  gap: 5px;
  font-size: 1.2rem;
}
label:hover {
  cursor: pointer;
}
.round {
  font-size: 2rem;
  font-weight: 600;
}
.gameContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 20px;
  min-height: 100vh;
}
.game {
  gap: 10px;
  display: grid;
  grid-template-columns: 200px 200px;
  grid-template-rows: 200px 200px;
}
.gameButton {
  transition: 0.3s ease-in-out;
  /* border: 2px solid wheat; */
}
.yellow {
  clip-path: circle(100% at 100% 100%);
  background-color: rgb(135, 135, 0);
}
.blue {
  clip-path: circle(100% at 0 100%);
  background-color: rgb(0, 0, 118);
}
.red {
  clip-path: circle(100% at 100% 0);
  background-color: rgb(116, 2, 2);
}
.green {
  clip-path: circle(100% at 0 0);
  background-color: rgb(0, 117, 0);
}

.active {
  /* filter: brightness(2); */
  animation: fade ease-in-out 0s infinite normal forwards;
}
.play {
  animation-play-state: running;
}
@keyframes fade {
  0%,
  100% {
    filter: brightness(1);
  }
  20%,
  80% {
    filter: brightness(2);
  }
}
</style>
