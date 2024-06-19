<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevYear">«</button>
      <button @click="prevMonthSingle">‹</button>
      <span>{{ formattedDate }}</span>
      <button @click="nextMonthSingle">›</button>
      <button @click="nextYear">»</button>
    </div>
    <div class="calendar-body">
      <div class="day-of-week" v-for="day in daysOfWeek" :key="day">{{ day }}</div>
      <div class="day" v-for="day in daysInMonth" :key="day.date">
        <div
          :class="dayClass(day)"
          @click="selectDate(day.date)"
        >{{ day.date.getDate() }}</div>
      </div>
    </div>
    <div class="time-picker">
      <div class="time-picker-row">
        <label>开始时间</label>
        <input type="time" v-model="startTime">
      </div>
      <div class="time-picker-row">
        <label>结束时间</label>
        <input type="time" v-model="endTime">
      </div>
    </div>
  </div>
</template>

<script>
import { addMonths, addYears, eachDayOfInterval, endOfMonth, endOfWeek, format, isSameDay, isSameMonth, isToday, isWithinInterval, startOfMonth, startOfWeek, subMonths, subYears } from 'date-fns';
import { computed, ref } from 'vue';

export default {
  name: 'CalendarTimePicker',
  setup() {
    const currentDate = ref(new Date());
    const selectedDateRange = ref([]);
    const startTime = ref('23:36');
    const endTime = ref('23:36');

    const daysOfWeek = ['S', 'M', 'T', 'W', 'T', 'F', 'S'];

    const formattedDate = computed(() => format(currentDate.value, 'yyyy年 MM月'));

    const daysInMonth = computed(() => {
      const start = startOfMonth(currentDate.value);
      const end = endOfMonth(currentDate.value);
      const startWeekDay = startOfWeek(start);
      const endWeekDay = endOfWeek(end);

      return eachDayOfInterval({ start: startWeekDay, end: endWeekDay }).map(date => ({
        date,
        isToday: isToday(date),
        isThisMonth: isSameMonth(date, currentDate.value),
        startDay: selectedDateRange.value.length === 1 && isSameDay(date, selectedDateRange.value[0]),
        isSelected: selectedDateRange.value.length && isWithinInterval(date, { start: selectedDateRange.value[0], end: selectedDateRange.value[1] }),
        isInRange: selectedDateRange.value.length === 2 && isWithinInterval(date, { start: selectedDateRange.value[0], end: selectedDateRange.value[1] }) && !isSameDay(date, selectedDateRange.value[0]) && !isSameDay(date, selectedDateRange.value[1])
      }));
    });

    const dayClass = (day) => {
      return {
        'day-inner': true,
        'day-today': day.isToday,
        'day-month': day.isThisMonth,
        'day-selected': day.isSelected,
        'day-in-range': day.isInRange,
        'day-start': day.startDay,
      };
    };

    const selectDate = (date) => {
      if (selectedDateRange.value.length === 0 || selectedDateRange.value.length === 2) {
        selectedDateRange.value = [date];
      } else {
        selectedDateRange.value = [selectedDateRange.value[0], date].sort((a, b) => a - b);
      }
    };

    const prevYear  = () => {
      currentDate.value = subYears(currentDate.value, 1);
    };

    const nextYear = () => {
      currentDate.value = addYears(currentDate.value, 1);
    };

    const prevMonthSingle = () => {
      currentDate.value = subMonths(currentDate.value, 1);
    };

    const nextMonthSingle = () => {
      currentDate.value = addMonths(currentDate.value, 1);
    };

    return {
      formattedDate,
      daysOfWeek,
      daysInMonth,
      selectDate,
      dayClass,
      prevYear,
      nextYear,
      prevMonthSingle,
      nextMonthSingle,
      startTime,
      endTime
    };
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  height: 100%;
  font-family: 'Arial', sans-serif;
  background-color: #f5f5f5;
}

.calendar {
  width: 30%;
  max-width: 400px;
  margin: 30px auto;
  border: 1px solid #ddd;
  align-items: center;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 90px;
  background-color: #f5f5f5;
  border-bottom: 1px solid #ddd;
  font-size: 18px;
  color: #333;
}

.calendar-header button {
  background: none;
  border: none;
  font-size: 27px;
  cursor: pointer;
  color: #333;
}

.calendar-body {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  width: 100%;
  gap: 10px;
  padding: 25px;
  /* background-color: #fff; */
}

.day-of-week {
  text-align: center;
  align-items: center;
  padding: 1px;
  font-size: 16px;
  color: #5c5c5c;
}

.day {
  text-align: center;
  padding: 1px;
  font-size: 16px;
  color: #c2c2c2;
}

.day-month {
  text-align: center;
  padding: 1px;
  font-size: 16px;
  color: #000000;
}

.day-inner {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  position: relative;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s;
}

.day-inner:hover {
  background-color: #eaeaea;
}

.day-today {
  border: 2px solid #6A1B9A;
  color: #6A1B9A;
}

.day-start {
  background-color: #6A1B9A;
  color: #fff;
}

.day-selected {
  background-color: #6A1B9A;
  color: #fff;
}

.day-in-range {
  background-color: #dfdddd;
}

.day-in-range::before {
  content: '';
  position: absolute;
  width: 300%;
  height: 35px;
  background-color: #faf3fe;
  z-index: -1;
}
rgb(236, 236, 236)rgb(236, 236, 236)
.time-picker {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  margin-bottom: 10px auto;
}

.time-picker-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 80%;
  margin: 10px auto;
}

.time-picker-row label {
  font-size: 16px;
  color: #333;
}

.time-picker-row input {
  width: 100px;
  height: 30px;
  text-align: center;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 5px;
}
</style>