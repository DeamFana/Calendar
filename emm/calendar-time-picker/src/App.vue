<template>
  <div id="app">
    <CalendarTimePicker />
  </div>
</template>

<script>
import CalendarTimePicker from './components/CalendarTimePicker.vue';

export default {
  name: 'App',
  components: {
    CalendarTimePicker
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>