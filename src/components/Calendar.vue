<template>
  <div class="container">
    <div class="header">
      <button @click="changeMonth(-1)">Previous Month</button>
      <span>Month: {{ selectedMonth }}</span>
      <span>Year: {{ selectedYear }}</span>
      <button
        @click="changeMonth(1)"
        :disabled="isNextMonthInFuture"
        :class="{ 'red-button': isNextMonthInFuture }"
      >
        Next Month
      </button>
      <div>
        <p>User's Local Date: {{ currentLocalDate }}</p>
      </div>
    </div>

    <div class="days-container">
      <div class="day-button">
        <button v-for="day in daysOfMonth" :key="day">{{ day }}</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';

// Get user's current date information (used for checking if next month is in the future and which date to stop at)
const currentLocalDate = new Date().toLocaleDateString();
const parsedDate = new Date(currentLocalDate);
const currentYear = parsedDate.getFullYear();
const currentMonth = parsedDate.getMonth() + 1; // Add 1 because month is 0-indexed
const currentDay = parsedDate.getDate();

// Selected month (for navigation)
const selectedMonth = ref(currentMonth);
const selectedYear = ref(currentYear);

// Get days in selected month
const daysOfMonth = computed(() => {
  // If current month only show till the current date
  if (selectedMonth.value === currentMonth && selectedYear.value === currentYear) {
    return currentDay;
  } else {
    const lastDayOfMonth = new Date(selectedYear.value, selectedMonth.value, 0).getDate();
    return lastDayOfMonth;
  }
});

const days = daysOfMonth.value;

// Change month
const changeMonth = (value) => {
  // If current month is 1 (January) and we click -1 (previous month), change month to 12 (December)
  if (selectedMonth.value === 1 && value === -1) {
    selectedMonth.value = 12;
    selectedYear.value = selectedYear.value - 1;
  }
  // If current month is 12 (December) and we click +1 (next month), change month to 12 (December)
  else if (selectedMonth.value === 12 && value === 1) {
    selectedMonth.value = 1;
    selectedYear.value = selectedYear.value + 1;
  } else {
    selectedMonth.value = selectedMonth.value + value;
  }
};

// Check if next month is the future (prevent entry for future months)
const isNextMonthInFuture = computed(() => {
  const nextMonth = selectedMonth.value + 1;
  const nextYear = selectedYear.value;
  return nextYear > currentYear || (nextYear === currentYear && nextMonth > currentMonth);
});
</script>

<style>
.container {
  padding: 30px;
}

.red-button {
  background-color: red;
  color: white;
}
</style>
