<template>
  <div id="app">
    <Calendar :events="events"/>
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  },
  data() {
    return {
      events: [
        {id: 1, name: 'Meeting Meeting Meeting', date: '2022-04-06 10:30', type: 'green'},
        {id: 2, name: 'Seminar', date: '2022-04-06 09:30', type: 'red'},
        {id: 3, name: 'Lesson', date: '2022-04-08 12:00', type: 'orange'},
        {id: 4, name: 'Call', date: '2022-04-14 16:30', type: 'green'},
        {id: 5, name: 'Demo', date: '2022-04-14 09:00', type: 'red'},
        {id: 6, name: 'Walk', date: '2022-04-10 14:00', type: 'orange'},
        {id: 7, name: 'Walk Walk Walk Walk', date: '2022-04-22 13:30', type: 'orange'},
      ]
    }
  }
}
</script>

<style>
body {
  background-color: #F5F5F5;
}
#app {
  margin-top: 40px;
}
</style>
