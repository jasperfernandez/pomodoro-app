<script setup lang="ts">
import { WORK_TIME, BREAK_TIME } from './constants';
import { ref, computed, onUnmounted } from 'vue';

const timeLeft = ref(WORK_TIME);
const isTimerRunning = ref(false);

let interval: number | undefined;

const formattedTime = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60)
    .toString()
    .padStart(2, '0');
  const seconds = (timeLeft.value % 60).toString().padStart(2, '0');
  return `${minutes}:${seconds}`;
});

const startTimer = () => {
  playWorkStarts();
  if (isTimerRunning.value) return;

  isTimerRunning.value = true;

  interval = setInterval(() => {
    if (timeLeft.value <= 0) {
      clearInterval(interval);
      isTimerRunning.value = false;
      timeLeft.value = BREAK_TIME;
      playBreakStarts();
      startTimer();
    } else {
      timeLeft.value--;
    }
  }, 1000);
};

const stopTimer = () => {
  if (interval !== undefined) {
    clearInterval(interval);
  }
  isTimerRunning.value = false;
};

const resetTimer = () => {
  stopTimer();
  timeLeft.value = WORK_TIME;
};

const playBreakStarts = () => {
  const audio = new Audio('/sounds/break-starts.mp3');
  audio.play();
};

const playWorkStarts = () => {
  const audio = new Audio('/sounds/work-starts.mp3');
  audio.play();
};

onUnmounted(() => {
  clearInterval(interval);
});
</script>

<template>
  <main>
    <h1>Pomodoro</h1>

    <p class="time">
      {{ formattedTime }}
    </p>

    <div class="controls">
      <button @click="startTimer" :disabled="isTimerRunning">Start</button>
      <button @click="stopTimer" :disabled="!isTimerRunning">Stop</button>
      <button class="secondary" @click="resetTimer">Reset</button>
    </div>
  </main>
</template>

<style>
main {
  max-width: 800px;
  margin: 1rem auto;
}

.time {
  font-size: 3rem;
  font-weight: bold;
  text-align: center;
  margin: 2rem 0;
}

.controls {
  display: flex;
  gap: 1rem;
  justify-content: center;
  align-items: center;
}
</style>
