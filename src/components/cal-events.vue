<template>
  <div class="events-wrapper" :style="bgColor">

        <div style="position: relative" v-if="dayEvents.events.length > 0">
          <img @click="preMonth(dayEvents.date)" src="https://s31.postimg.org/4fqn98kff/left.png" alt="Left" class="ra-img-left">
          <img @click="nextMonth(dayEvents.date)" src="https://s31.postimg.org/z9xw6z5hn/right.png" alt="Right" class="ra-img-right">
        </div>

        <div style="position: relative" v-if="dayEvents.events.length == 0">
          <img @click="preMonth('17')" src="https://s31.postimg.org/4fqn98kff/left.png" alt="Left2" class="ra-img-left">
          <img @click="nextMonth('17')" src="https://s31.postimg.org/z9xw6z5hn/right.png" alt="Right2" class="ra-img-right">
        </div>
    <div class="cal-events">
      <slot>
        <div v-for="(event, index) in events" class="event-item">
            <cal-event-item :event="event" :index="index" :locale="locale"></cal-event-item>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
import i18n from '../i18n.js'
import { dateTimeFormatter } from '../tools.js'
import calEventItem from './cal-event-item.vue'
export default {
  name: 'cal-events',
  data () {
    return {
      i18n,
      eve: []
    }
  },
  components: {
    'cal-event-item': calEventItem
  },
  props: {
    title: String,
    dayEvents: {
      type: Object,
      required: true
    },
    locale: {
      type: String,
      required: true
    },
    color: {
      type: String,
      required: true
    }
  },
  computed: {
    dayEventsTitle () {
      if (this.title) return this.title
      if (this.dayEvents.date !== 'all') {
        let tempDate
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(this.dayEvents.events[0].date))
          return dateTimeFormatter(tempDate, i18n[this.locale].fullFormat)
        } else {
          tempDate = dateTimeFormatter(Date.parse(new Date(this.dayEvents.date)), i18n[this.locale].fullFormat)
          return `${tempDate} ${i18n[this.locale].notHaveEvents}`
        }
      } else {
        return i18n[this.locale].dayEventsTitle
      }
    },
    events () {
      return this.dayEvents.events
    },
    bgColor () {
      return {backgroundColor: this.color}
    }
  },
  created () {
    axios.get(window.jsonurl)
    .then(response => {
      this.eve = response.data;
    })
    .catch(e => {
      console.log(e)
    })
  },
  methods: {
    dateTimeFormatter,
    nextMonth (t) {
      /*this.$EventCalendar.nextMonth()
      this.$emit('month-changed', this.curYearMonth)*/
      if (t == '17') {
        var today1 = new Date();
        var today2 = `${today1.getFullYear()}/${today1.getMonth()+1}/${today1.getDate()}`;
        var today3day = `${today1.getDate()}`;
        var today4mon = `${today1.getMonth()+1}`;

        for (var i = 0; i < this.eve.length; i++) {
          if (this.eve[i].date.split('/')[2] > today3day && this.eve[i].date.split('/')[1] == today4mon) {
              var te = i + 1;
              this.$emit('cur-day-changed', this.eve[te].date);
              break;
          }
          if (this.eve[i].date.split('/')[2] < today3day && this.eve[i].date.split('/')[1] != today4mon) {
              var te = i + 1;
              this.$emit('cur-day-changed', this.eve[te].date);
              this.$EventCalendar.nextMonth()
              this.$emit('month-changed', this.eve[te].date.split('/')[1])
              break;
          }
        }
      } else {
        for (var i = 0; i < this.eve.length; i++) {
          if (t == this.eve[i].date) {
            var tr = i+1;
            this.$emit('cur-day-changed', this.eve[tr].date);
            if (this.eve[i].date.split('/')[1] != this.eve[tr].date.split('/')[1]) {
              this.$EventCalendar.nextMonth()
              this.$emit('month-changed', this.eve[i].date.split('/')[1])
            }
            break;
          }
        }
      }
    },
    preMonth (t) {
      /*this.$EventCalendar.preMonth()
      this.$emit('month-changed', this.curYearMonth)*/
      if (t == '17') {
        var today1 = new Date();
        var today2 = `${today1.getFullYear()}/${today1.getMonth()+1}/${today1.getDate()}`;
        var today3day = `${today1.getDate()}`;
        var today4mon = `${today1.getMonth()+1}`;

        for (var i = 0; i < this.eve.length; i++) {
          if (this.eve[i].date.split('/')[2] < today3day && this.eve[i].date.split('/')[1] == today4mon) {
              var te = i;
              this.$emit('cur-day-changed', this.eve[te].date);
              break;
          }
          if (this.eve[i].date.split('/')[2] > today3day && this.eve[i].date.split('/')[1] != today4mon) {
              var te = i;
              this.$emit('cur-day-changed', this.eve[te].date);
              this.$EventCalendar.preMonth()
              this.$emit('month-changed', this.eve[te].date.split('/')[1])
              break;
          }
        }
      } else {
        for (var i = 0; i < this.eve.length; i++) {
          if (t == this.eve[i].date) {
            var tr = i-1;
            this.$emit('cur-day-changed', this.eve[tr].date);
            if (this.eve[i].date.split('/')[1] != this.eve[tr].date.split('/')[1]) {
              this.$EventCalendar.preMonth()
              this.$emit('month-changed', this.eve[i].date.split('/')[1])
            }
            break;
          }
        }
      }
    },
  }
}
</script>

<style>
  .ra-img-left {
    position: absolute;
    left: -80px;
    top:200px;
    z-index: 200;
  }
  .ra-img-right {
    position: absolute;
    right: -80px;
    top:200px;
    z-index: 200;
  }
  @media screen and (min-width: 769px) {
  }
  @media screen and (max-width: 900px) {
    .ra-img-left {
      position: absolute;
      left: 10px;
      top:200px;
      z-index: 200;
    }
    .ra-img-right {
      position: absolute;
      right: 10px;
      top:200px;
      z-index: 200;
    }
  }
</style>