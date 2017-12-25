<template>
  <div class="__vev_calendar-wrapper">
    <cal-panel
      :events="events"
      :calendar="calendarOptions"
      :selectedDay='selectedDayEvents.date'
      @cur-day-changed="handleChangeCurDay"
      @month-changed="handleMonthChanged">
    </cal-panel>
    <cal-events
      :title="title"
      :dayEvents="selectedDayEvents"
      :locale="calendarOptions.options.locale"
      :color="calendarOptions.options.color"
      @cur-day-changed="handleChangeCurDay"
      @month-changed="handleMonthChanged">
      <slot :showEvents="selectedDayEvents.events"></slot>
    </cal-events>
  </div>
</template>

<script>
import { isEqualDateStr} from './tools.js'


import calEvents from './components/cal-events.vue'
import calPanel from './components/cal-panel.vue'

var dateOb = new Date()

const inBrowser = typeof window !== 'undefined'
export default {
  name: 'vue-event-calendar',
  components: {
    'cal-events': calEvents,
    'cal-panel': calPanel
  },
  data () {
    return {
      selectedDayEvents: {
        date: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`,
        events: []
      }
    }
  },
  props: {
    title: String,
    events: {
      type: Array,
      required: true,
      default: [],
      validator (events) {
        let validate = true
        events.forEach((event, index) => {
          if (!event.date) {
            console.error('Vue-Event-Calendar-Error:' + 'Prop events Wrong at index ' + index)
            validate = false
          }
        })
        return validate
      }
    }
  },
  computed: {
    calendarOptions () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA
      } else {
        return {
          options: {
            locale: 'ru', //zh
            color: '#ffb24a'
          },
          params: {
              curYear: dateObj.getFullYear(),
              curMonth: dateObj.getMonth(),
              curDate: dateObj.getDate(),
              curEventsDate: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`
          }
        }
      }
    },
    calendarParams () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA.params
      } else {
        return {
          curYear: dateObj.getFullYear(),
          curMonth: dateObj.getMonth(),
          curDate: dateObj.getDate(),
          curEventsDate: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`
        }
      }
    }
  },
  created () {
      this.handleChangeCurDay(`${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`)
  },
  methods: {
    handleChangeCurDay (date) {
      let events = this.events.filter(function(event) {
        return isEqualDateStr(event.date, date)
      })
      if (events.length > 0) {
        this.selectedDayEvents = {
          date: date,
          events: events.slice(0, 1)
        }
      }
      this.$emit('day-changed', {
        date: date,
        events: events.slice(0, 1)
      })
    },
    handleMonthChanged (yearMonth) {
      this.$emit('month-changed', yearMonth)
    }
  },
  watch: {
    calendarParams () {
      if (this.calendarParams.curEventsDate !== 'all') {
        let events = this.events.filter(event => {
          return isEqualDateStr(event.date, `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`)
        })
        this.selectedDayEvents = {
          date: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`,
          events
        }
      } else {
        this.selectedDayEvents = {
          date: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`,
          events: this.events.slice(0, 1)
        }
      }
    },
    events () {
      var eve333 = this.events.filter(function(event) {
        return isEqualDateStr(event.date, `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`)
      })
      this.selectedDayEvents = {
        date: `${dateOb.getFullYear()}/${dateOb.getMonth()+1}/${dateOb.getDate()}`,
        events: eve333.slice(0, 1)
      }
    }
  }
}
</script>
<style lang="less">
@base-orange: rgb(255, 178, 74);
@white: #ffffff;
@gray: #e0e0e0;
@gray-dark: #b1b1b1;
@large-padding: 15px;
@small-padding: 10px;

@icon-border-size: 1px;
@media screen and (min-width: 895px) {
  .__vev_calendar-wrapper{
    max-width: 1200px;
    margin: 0 auto;
    .cal-wrapper{
      width: 50%;
      margin-top:50px;
      margin-bottom:50px;
      padding-top: 30px;
      padding-bottom: 30px;
      padding-left: 50px;
      padding-right: 50px;
      background-color: #f8f8f8;
      .date-num{
        line-height: 50px;
      }
    }
    .events-wrapper{
      width: 47%;
      background-color: @base-orange;
      color: @white;
      padding: 40px 45px;
      position: absolute;
      left: 50%;
      top: 0;
      bottom: 0;
    }
  }
}
@media screen and (max-width: 895px) {
  .__vev_calendar-wrapper{
    .cal-wrapper{
      width: 100%;
      padding: 10px 5px;
      .date-num{
        line-height: 42px;
      }
    }
    .events-wrapper{
      width: 100%;
      margin-top: 10px;
      padding: 10px;
    }
  }
}
.__vev_calendar-wrapper{
  font-family: 'PlayfairDisplay';
  position: relative;
  overflow: hidden;
  width: 100%;
  *{
    box-sizing: border-box;
  }
  ::-webkit-scrollbar{
    width: 8px;
    height: 8px;
  }
  ::-webkit-scrollbar-track {
    box-shadow: inset 0 0 2px rgba(0,0,0,.2);
    border-radius: 5px;
  }
  ::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background: rgba(0,0,0,.2);
  }
  .cal-wrapper{
    .cal-header{
      position: relative;
      width: 100%;
      background-color: #f8f8f8;
      // box-shadow: 0 6px 5px rgba(0,0,0,.1);
      font-weight: 500;
      overflow: hidden;
      padding-bottom: 10px;
      &>div{
        float: left;
        line-height: 20px;
        padding: @large-padding;
      }
      .title{
        width: 60%;
        text-align: center;
      }
      .l{
        text-align: left;
        width: 40%;
        cursor: pointer;
        user-select: none;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
      }
      .r{
        text-align: right;
        width: 40%;
        cursor: pointer;
        user-select: none;
        -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
      }
    }
    .cal-body{
      width: 100%;
      .weeks{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1.2rem;
        color: #4b8078;
        .item{
          line-height: 50px;
          float: left;
          width: 14.285%;
        }
      }
      .dates{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1rem;
        color: #4b8078;
        .item{
          position: relative;
          float: left;
          display: block;
          width: 14.285%;
          cursor: default;
          -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
          .date-num{
            font-size: 1.2rem;
            position: relative;
            z-index: 3;
          }
          &.event{
            color: white;
            cursor: pointer;
          }
          &.selected-day{
            .is-event{
              background-color: @base-orange;
            }
          }
          .is-event{
            content: '';
            background-color: @base-orange;
            color: white;
            border-radius: 50%;
            width: 36px;
            height: 36px;
            position: absolute;
            left: 50%;
            top: 50%;
            z-index: 1;
            margin-left: -18px;
            margin-top: -16px;
          }
          .is-today{
            width: 12px;
          }
        }
      }
    }
  }
  .events-wrapper{
    border-radius: 10px;
    .cal-events{
      height: 100%;
      overflow-y: auto;
      padding: 0 5px;
      margin: 15px 0;
    }
    .date{
      max-width: 60%;
      min-width: 200px;
      text-align: center;
      color: @white;
      background-color: rgba(0, 0, 0, 0.2);
      border-radius: 20px;
      margin: 0 auto;
      font-size: 22px;
    }
    .event-item{
      padding: 5px 20px;
      margin-top: 15px;
      box-shadow: 0 3px 11px 2px rgba(0,0,0,.1);
      background-color: #fff;
      border-radius: 5px;
      color: #323232;
      position: relative;
      &:first-child{
        margin-top: 0;
      }
      .title{
        height: 40px;
        line-height: 40px;
        color: #323232;
        font-size: 16px;
        border-bottom: 1px solid #f2f2f2;
      }
      .time{
        position: absolute;
        right: 30px;
        top: 17px;
        color: #9b9b9b;
        font-size: 14px;
      }
      .desc{
        color: #9b9b9b;
        font-size: 14px;
        padding: 7px 0;
      }
    }
  }
  .arrow-left.icon {
    color: #000;
    position: absolute;
    left: 6%;
    margin-top: 10px;
  }
  .arrow-left.icon:before {
    content: '';
    position: absolute;
    left: 1px;
    top: -5px;
    width: 10px;
    height: 10px;
    border-top: solid @icon-border-size currentColor;
    border-right: solid @icon-border-size currentColor;
    -webkit-transform: rotate(-135deg);
            transform: rotate(-135deg);
  }
  .arrow-right.icon {
    color: #000;
    position: absolute;
    right: 6%;
    margin-top: 10px;
  }
  .arrow-right.icon:before {
    content: '';
    position: absolute;
    right: 1px;
    top: -5px;
    width: 10px;
    height: 10px;
    border-top: solid @icon-border-size currentColor;
    border-right: solid @icon-border-size currentColor;
    -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
  }
  h3, p{
    margin: 0;
    padding: 0;
  }
}

</style>
