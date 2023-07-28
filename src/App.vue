<template>
  <div class="card">
    <div class="container">
      <div class="formClass">
        <div class="form_container">
          <div class="block">
            <label for="day"> Day </label>
            <input type="number" placeholder="DD" id="day" v-model="dayInput" />
            <small>{{ errors.day }}</small>
          </div>
          <div class="block">
            <label for="month"> Month </label>
            <input
              type="number"
              placeholder="MM"
              id="month"
              v-model="monthInput"
            />
            <small>{{ errors.month }}</small>
          </div>
          <div class="block">
            <label for="year"> Year </label>
            <input
              type="number"
              placeholder="YYYY"
              id="year"
              v-model="yearInput"
            />
            <small>{{ errors.year }}</small>
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

const errors = ref({
  day: null,
  month: null,
  year: null,
});

function validateInput() {
  errors.value = {
    day: null,
    month: null,
    year: null,
  };

  if (!dayInput.value) {
    errors.value.day = "Day is required";
  } else if (dayInput.value < 1 || dayInput.value > 31) {
    errors.value.day = "Day must be between 1 and 31";
  }

  if (!monthInput.value) {
    errors.value.month = "Month is required";
  } else if (monthInput.value < 1 || monthInput.value > 12) {
    errors.value.month = "Month must be between 1 and 12";
  }

  if (!yearInput.value) {
    errors.value.year = "Year is required";
  } else if (yearInput.value > new Date().getFullYear()) {
    errors.value.year = "Year cannot be in the future";
  }

  // Check for invalid date
  if (
    yearInput.value &&
    monthInput.value &&
    dayInput.value &&
    !isValidDate(dayInput.value, monthInput.value, yearInput.value)
  ) {
    errors.value.day = "Invalid date";
  }

  return Object.values(errors.value).every((error) => error === null);
}

function isValidDate(day, month, year) {
  const inputDate = new Date(year, month - 1, day);
  return (
    inputDate.getDate() === Number(day) &&
    inputDate.getMonth() + 1 === Number(month) &&
    inputDate.getFullYear() === Number(year)
  );
}

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
  if (!validateInput()) {
    return;
  }
  const yearToNumber = Number(yearInput.value);
  const monthToNumber = Number(monthInput.value) - 1; // Adjust for JavaScript's 0-indexed months
  const dayToNumber = Number(dayInput.value);
  const date = new Date(yearToNumber, monthToNumber, dayToNumber);
  const currentDate = new Date();

  let yearsDiff = currentDate.getFullYear() - yearToNumber;
  let monthsDiff = currentDate.getMonth() - monthToNumber;
  let daysDiff = currentDate.getDate() - dayToNumber;

  if (monthsDiff < 0 || (monthsDiff === 0 && daysDiff < 0)) {
    yearsDiff--; // Subtract a year if the upcoming month/date hasn't been reached yet
    monthsDiff += 12; // Add 12 to monthsDiff to handle negative values
  }

  if (daysDiff < 0) {
    monthsDiff--; // Subtract a month if the upcoming date hasn't been reached yet
    let previousMonthLastDay = new Date(
      currentDate.getFullYear(),
      currentDate.getMonth(),
      0
    ).getDate();
    daysDiff += previousMonthLastDay; // Add the number of days in the previous month to daysDiff to handle negative values
  }

  years.value = yearsDiff;
  months.value = monthsDiff;
  days.value = daysDiff;

  // Animate values
  await Promise.all([
    animateValue(years, years.value),
    animateValue(months, months.value),
    animateValue(days, days.value),
  ]);
}
</script>
