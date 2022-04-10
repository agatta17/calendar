<template>
  <div class="calendar-wrap">
    <div class="control">
      <div class="control__arrow control__arrow_left" @click="subtractMonth"></div>
      <div class="control__text">
        <span>{{printMonth(month)}}</span>
        <span v-if="year !== todayYear">{{`&nbsp;${year}`}}</span>
      </div>
      <div class="control__arrow control__arrow_right" @click="addMonth"></div>
    </div>
    <div class="calendar">
      <div class="calendar__day-name">ПН</div>
      <div class="calendar__day-name">ВТ</div>
      <div class="calendar__day-name">СР</div>
      <div class="calendar__day-name">ЧТ</div>
      <div class="calendar__day-name">ПТ</div>
      <div class="calendar__day-name">СБ</div>
      <div class="calendar__day-name">ВС</div>
      <div class="calendar__date date" v-for="(date, i) in dates" :key="i" 
        :class="getStylesOfDate(date)">
        <div class="date__number">
          {{getDayOfMonth(date)}}
        </div>
        <ul class="date__events">
          <li v-for="event in eventsIndexed[formatDateToString(date)]" :key="event.id" 
            class="date__event" :class="`date__event_${event.type}`">
            {{`${event.time} ${event.name}`}}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
import { groupBy, sortBy } from 'lodash';
export default {
  name: 'Calendar',
  props: {
    events: Array
  },
  data() {
    return {
      today: null,
      month: 0,
      year: 0,
    }
  },
  computed: {
    eventsIndexed() {
      let result  = sortBy(this.events, ['date']);
      result = result.map(item => {
        const [date,time] = item.date.split(' ');
        return {...item, date, time};
      })
      return groupBy(result, 'date');
    },
    todayYear() {
      return moment().year();
    },
    firstDayOfMonth() {
      return moment().year(this.year).month(this.month).startOf('month');
    },
    firstDisplayedDay() {
      return moment(this.firstDayOfMonth).subtract(this.firstDayOfMonth.isoWeekday() - 1, 'days');
    },
    lastDayOfMonth() {
      return moment(this.firstDayOfMonth).endOf('month');
    },
    lastDisplayedDay() {
      return moment(this.lastDayOfMonth).add(7 - this.lastDayOfMonth.isoWeekday(), 'days');
    },
    dates() {
      const dates = [];
      let date = this.firstDisplayedDay;
      while (date.isSameOrBefore(this.lastDisplayedDay)) {
        dates.push(date);
        date = moment(date).add(1, 'days')
      }
      return dates
    }
  },
  methods: {
    getDayOfMonth(date) {
      return date.date();
    },
    formatDateToString(date) {
      return moment(date).format('YYYY-MM-DD');
    },
    addMonth() {
      this.month++;
      if (this.month > 11) {
        this.month = 0;
        this.year++;
      }
    },
    subtractMonth() {
      this.month--;
      if (this.month < 0) {
        this.month = 11;
        this.year--;
      }
    },
    printMonth(number) {
      return moment().month(number).locale('ru').format('MMMM').toUpperCase();
    },
    getStylesOfDate(date) {
      const dayOfWeek = date.isoWeekday();
      const isHoliday = dayOfWeek === 6 || dayOfWeek === 7;
      return {
        'calendar__date_before': date.isBefore(this.today), 
        'calendar__date_holiday': isHoliday,
        'calendar__date_today': moment(date).isSame(this.today)
      }
    }
  },
  mounted() {
    this.today = moment().startOf('day');
    this.month = moment().month();
    this.year = this.todayYear;
  }
}
</script>

<style scoped>
.calendar-wrap {
  background-color: #FFF;
  max-width: 900px;
  border-radius: 15px;
  padding: 12px;
  margin: 0 auto;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: 600;
  font-size: 14px;
  color: #595959;
}
.control {
  margin-bottom: 20px;
  color: #4682B4;
  font-size: 16px;
  display: flex;
}
.control__arrow {
  width: 8px;
  height: 8px;
  border-top: 2px solid #595959;
  border-right: 2px solid #595959;
  cursor: pointer;
  margin: 4px 6px;
}
.control__arrow_right {
  transform: rotate(45deg);
}
.control__arrow_left {
  transform: rotate(-135deg);
}
.control__text {
  width: 140px;
  text-align: center;
}
.calendar {
  display: grid;
  grid-template-columns: repeat(7,minmax(13%, 1fr));
  grid-gap: 3px;
}
.calendar__day-name {
  text-align: center;
}
.date {
  border: 2px solid #F5F5F5;
  border-radius: 8px;
  height: 90px;
  padding: 8px;
  position: relative;
}
.date__number {
  text-align: end;
  margin-bottom: 4px;
}
.calendar__date_before {
  color: #B1B1B1;
  background-color: #F7F7F7;
}
.calendar__date_holiday {
  color: #ABBBE0;
}
.calendar__date_today {
  color: #92C199;
  border-color: #BEBEBE;
}
.date__events {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
.date__event {
  border-radius: 6px;
  padding: 2px 4px;
  margin-bottom: 3px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  font-weight: 400;
  position: relative;
  z-index: 1;
}
.date__event:hover {
  overflow: visible;
  min-width: fit-content;
}
.date__event_orange {
  background-color: #F9EBDB;
  color: #E2BC89;
}
.date__event_red {
  background-color: #FAEDEC;
  color: #D67C88;
}
.date__event_green {
  background-color: #E8F2EA;
  color: #9BC8A6;
}
@media screen and (max-width: 768px) {
  .calendar-wrap {
    padding: 6px;
  }
  .date {
    padding: 2px 0;
  }
  .date__event {
    font-size: 8px;
  }
}
</style>