<template>
  <div class="card">
    <div class="container">
      <div class="formClass">
        <div class="form_container">
          <div class="block">
            <label for="day"> Day </label>
            <input type="number" placeholder="DD" id="day" v-model="dayInput" />
            <small></small>
          </div>
          <div class="block">
            <label for="month"> Month </label>
            <input
              type="number"
              placeholder="MM"
              id="month"
              v-model="monthInput"
            />
            <small></small>
          </div>
          <div class="block">
            <label for="year"> Year </label>
            <input
              type="number"
              placeholder="YYYY"
              id="year"
              v-model="yearInput"
            />
            <small></small>
          </div>
        </div>
        <div class="submit_block">
          <hr />
          <button class="submit_btn" @click="submitInput">
            <img src="./assets/images/icon-arrow.svg" alt="icon" />
          </button>
        </div>
      </div>
      <div class="output">
        <h1>
          <span id="YY">{{ years }}</span> years
        </h1>
        <h1>
          <span id="MM">{{ months }} </span> months
        </h1>
        <h1>
          <span id="DD">{{ days }}</span> days
        </h1>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from "vue";

const years = ref("--");
const months = ref("--");
const days = ref("--");

const monthInput = ref();
const yearInput = ref();
const dayInput = ref();

const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));

async function animateValue(ref, num) {
  let step = 50;
  num > 25 && (step = 35);
  num > 50 && (step = 25);
  num > 75 && (step = 20);
  num > 100 && (step = 10);
  num > 200 && (step = 1);

  let n = 0;
  if (num === 0) {
    ref.value = n;
  } else {
    while (n < num) {
      await delay(step);
      n++;
      ref.value = n;
    }
  }
}

async function submitInput() {
  const yearToNumber = Number(yearInput.value);
  const monthToNumber = Number(monthInput.value);
  const dayToNumber = Number(dayInput.value);
  const date = new Date(yearToNumber, monthToNumber - 1, dayToNumber);
  const currentDate = new Date();
  console.log(date);
  if (
    currentDate <
    new Date(currentDate.getFullYear(), monthToNumber - 1, dayToNumber)
  ) {
    years.value = currentDate.getFullYear() - date.getFullYear() - 1;
    months.value = currentDate.getMonth() + 1;
    days.value = currentDate.getDate() - dayToNumber;
  } else {
    if (currentDate.getMonth() + 1 === monthToNumber) {
      months.value = 0;
      days.value = currentDate.getDate() - dayToNumber;
    } else {
      months.value = currentDate.getMonth() + 1 - monthToNumber;
      if (currentDate.getDate() < dayToNumber) {
        months.value = months.value - 1;
        days.value =
          currentDate.getDate() -
          new Date(
            currentDate.getFullYear(),
            currentDate.getMonth(),
            0
          ).getDate() -
          dayToNumber;
      } else {
        days.value = currentDate.getDate() - dayToNumber;
      }
    }
    years.value = currentDate.getFullYear() - date.getFullYear();
  }

  // Animate values
  await Promise.all([
    animateValue(years, years.value),
    animateValue(months, months.value),
    animateValue(days, days.value),
  ]);
}
</script>
