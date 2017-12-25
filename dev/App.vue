<template>
  <div id="app">
    <vue-event-calendar :events="eve">
      <template scope="props">
        <div v-if="props.showEvents.length > 0">
          <div v-for="(event, index) in props.showEvents" class="ra-block" v-if="event.img != ''" v-bind:style="{ 'background-image': 'url(' + event.img + ')' }">
              <div>
                <span style="font-size:95px; font-weight:100; font-family: 'PlayfairDisplay';">{{event.day}}</span>
                <br>
                <span style="font-style: italic; font-family: 'PlayfairDisplay'; font-size: 18px;">{{event.mon}}</span>
                <br>
                <br>
                <br>
                <br>
                <p style="font-family: 'PF Beau Sans Pro'; font-weight: 400; font-size: 18px;">{{event.title}}</p>
              </div>
          </div>

          <div v-for="(event, index) in props.showEvents" class="ra-block" v-if="event.img == ''" v-bind:style="{ 'background-image': 'url(https://s31.postimg.org/4n9orm8gr/background.png)' }">
            <div>
              <span style="font-size:95px; font-weight:100; font-family: 'PlayfairDisplay';">{{event.day}}</span>
              <br>
              <span style="font-style: italic; font-family: 'PlayfairDisplay'; font-size: 18px;">{{event.mon}}</span>
              <br>
              <br>
              <br>
              <br>
              <p style="font-family: 'PF Beau Sans Pro'; font-weight: 400; font-size: 18px;">{{event.title}}</p>
            </div>
          </div>
        </div>
        <div v-if="props.showEvents.length < 1">
          <div v-for="(event, index) in eve" class="ra-block ra-block2" v-if="event.img != '' && event.date.split('/')[2] > todayday" v-bind:style="{ 'background-image': 'url(' + event.img + ')' }">
              <span style="font-size:95px; font-weight:100; font-family: 'PlayfairDisplay';">{{event.day}}</span>
              <br>
              <span style="font-style: italic; font-family: 'PlayfairDisplay'; font-size: 18px;">{{event.mon}}</span>
              <br>
              <br>
              <br>
              <br>
              <p style="font-family: 'PF Beau Sans Pro'; font-weight: 400; font-size: 18px;">{{event.title}}</p>
          </div>

          <div v-for="(event, index) in eve" class="ra-block ra-block2" v-if="event.img == '' && event.date.split('/')[2] > todayday" v-bind:style="{ 'background-image': 'url(https://s31.postimg.org/4n9orm8gr/background.png)' }">
              <span style="font-size:95px; font-weight:100; font-family: 'PlayfairDisplay';">{{event.day}}</span>
              <br>
              <span style="font-style: italic; font-family: 'PlayfairDisplay'; font-size: 18px;">{{event.mon}}</span>
              <br>
              <br>
              <br>
              <br>
              <p style="font-family: 'PF Beau Sans Pro'; font-weight: 400; font-size: 18px;">{{event.title}}</p>
          </div>
        </div>
      </template>
    </vue-event-calendar>
  </div>
</template>

<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script>
import axios from 'axios'
import '../src/fonts.css'

var dateOb2 = new Date()

export default {
  name: 'app',
  data () {
    return {
      eve: [],
      todayday: `${dateOb2.getDate()}`,
      todaymon: `${dateOb2.getMonth()+1}`,
      todayyear: `${dateOb2.getFullYear()}`
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
    handleDayChanged (data) {
    },
    handleMonthChanged (data) {
    }
  }
}
</script>

<style>
#app {
  font-family: 'PF Beau Sans Pro';
  font-weight: 700;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 30px;
}

.ra-block{
  color: black;
  width: 100%;
  height: 520px;
  margin: 0 auto;
  text-align:center;
  padding-top: 20%;
  font-family: 'PF Beau Sans Pro';
  background-repeat: no-repeat;
}

.ra-block2 {
  display: none;
}
.ra-block2:first-child {
  display: block;
}

h1, h2, h3 {
  font-weight: normal;
  margin: 0;
  padding: 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
.t-center{
  text-align: center;
  margin: 20px;
  font-family: 'PlayfairDisplay';
  font-style: italic;
  font-weight: 600;
  font-size: 42px;
  margin-bottom: 30px;
  color: #4b8078;
}
.t-center2{
  font-family: 'PlayfairDisplay';
  font-style: italic;
  font-weight: 600;
  font-size: 22px;
  color: #4b8078;
}
</style>
