<template>
  <div class='card'>
    <h3 class='month'>{{ getMonthNameByNumber(new Date()) }}</h3>
    <div class='day-names'>
      <div class='name' v-for='name in daysNames' :key="name">{{ name }}</div>
    </div>
    <div class='calendar'>
      <div
        :class="['day',
          {
            [day.dayOfWeek]: !index,
            currentMonth: day.month === currentMonth,
            practice: day.practice,
            participate: checkIfParticipating(day.practice),
            inQueue: isInQueue(currentUser, day.practice),
            today: day.isToday,
            past: day.isPast
          }
        ]"
        @click="togglePracticeDetails(day)"
        v-for="(day, index) in days"
        :key="day">
        <p class='dayNumber'>{{ day.day }}</p>
        <span class='practiceTime' v-if="day.practice">{{ day.practice.dateTime.time }}</span>
        <span class='ocupationPercent' v-if="day.practice" :style="{ top: checkOcupation(day.practice) + '%' } "/>
      </div>
    </div>
    <div class='legend-container'>
        <p class='legend'>Libre</p>
        <p class='legend full'>Completa</p>
        <p class='legend subscribed'>Suscrito</p>
        <p class='legend inQueue'>En cola</p>
        <p class='legend current-day'>Hoy</p>
    </div>
  </div>
  <PracticeCalendarDetails v-if="practiceDetails" :value="practiceDetails" :isParticipating="checkIfParticipating(practiceDetails)" @closeDetails="handleClose()" />
</template>

<script setup>
import { useStore } from 'vuex'
import { ref, onMounted, computed } from 'vue'
import { getDayOfWeek, getMonthNameByNumber, areEqualDates} from '@/services/util/time.js'
import { isInQueue } from '@/services/util/practice.js'
import PracticeCalendarDetails from '@/components/practice/PracticeCalendarDetails.vue'

const store = useStore()
const practiceID = ref(undefined)
const daysNames = ['L', 'M', 'X', 'J', 'V', 'S', 'D']
const currentUser = store.getters.getUser
const currentMonth = new Date().getMonth()
onMounted( () => store.dispatch('fetchPractices'))

const practicesAroundToday = computed( () => store.getters.getTwoWeeksAround('practices'))
const practiceDetails = computed( () => practicesAroundToday.value.find(p => p._id === practiceID.value) )

const days = computed( () => {
  const list = []
  const currDay = new Date(new Date().setHours(0, 0, 0, 0))
  const tempDay = new Date(currDay).setDate(currDay.getDate() - 14)
  const twoWeeksAfter = new Date(currDay).setDate(currDay.getDate() + 14)

  let loop = new Date(tempDay)
  while (loop <= twoWeeksAfter) {
    const practice = !practicesAroundToday.value ? undefined : practicesAroundToday.value.find(p => areEqualDates(new Date(p.dateTime.date).setHours(0, 0, 0, 0), loop))
    list.push({
      day: new Date(loop).getDate(),
      dayOfWeek: getDayOfWeek(loop),
      month: new Date(loop).getMonth(),
      practice: practice,
      isToday: areEqualDates(new Date().setHours(0, 0, 0, 0), loop),
      isPast: new Date().setHours(0, 0, 0, 0) > new Date(loop)
    })
    const newDate = loop.setDate(loop.getDate() + 1)
    loop = new Date(newDate)
  }
  return list
})

function checkOcupation (practice) { return 100 - practice.percentOcupied }
function checkIfParticipating (practice) { return (!practice) ? false : !!practice.players.find(p => p._id === currentUser._id) }
function handleClose () { practiceID.value = undefined }
function togglePracticeDetails (day) { if (day.practice) practiceID.value = (practiceID.value) ? undefined : day.practice._id }

</script>


<style lang="scss" scoped>
@import '@/colors.scss';
.card {
  box-shadow: 0 10px 40px $greySuperLight,  0 2px 4px rgba($blueDark, .4) ;
  border-radius: 4px;
  width: clamp(369px, 369px, 400px);
  margin: 0 auto;
  .month {
    margin: 0.2em 0;
    font-weight: 200;
    color: rgba($blueDark, .6);
  }
}
.day-names,
.calendar {
  display: grid;
  grid-template-areas: "Monday Tuesday Wednesday Thursday Friday Saturday Sunday";
  grid-template-columns: repeat(7, 48px);
  column-gap: 4px;
  row-gap: 4px;
  padding: 4px;
}
.calendar {
  grid-template-rows: repeat(auto, 48px);
  border-top: 1px solid rgba($blueDark, .2);
  border-bottom: 1px solid rgba($blueDark, .4);
  background-color: rgba($greySuperLight, .1);
}
.day-names {
  padding: 0 4px;
  border-top: 1px solid rgba($blueDark, .4);
  background-color: rgba($greyLight, .1);
  .name {
    text-align: center;
  }
}
.day {
  display: inline-flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  color: rgba($black, .2);
  position: relative;
  overflow: hidden;
  pointer-events: none;

  &.participate{
      color: #f9f9f9;
      border: 2px solid $dangerous;
      .ocupancy{
          background-color: $blueLight;
      }
  }
  &.inQueue {
    border: 2px solid $blueDark;
  }
  * {
    margin: 0;
  }
  &:focus,
  &:active {
    border: 1px dashed;
    transition: .475s ease-in-out;
  }
  &.currentMonth {
    color: $black;
  }
  &.today {
    color: red;
    font-weight: 900;
  }
  &.past {
    filter: grayscale(.8);
  }
  &.practice {
    cursor: pointer;
    pointer-events: all;
    // border: 1px solid $blueDark;
    &::before {
      background-color: rgb(241, 188, 241);
    }
  }
  .practiceTime {
    font-size: .8em;
    color: rgba($black, .4);
  }
  &::before,
  .ocupationPercent {
    content: '';
    width: 100%;
    height: 100%;
    z-index: -2;
    position: absolute;
  }
  .ocupationPercent {
    background-color: $blueLight;
  }
  &.Lunes {
    // grid-area: Monday;
    grid-column: 1;
  }
  &.Martes {
    // grid-area: Tuesday;
    grid-column: 2;
  }
  &.Miercoles {
    // grid-area: Wednesday;
    grid-column: 3;
  }
  &.Jueves {
    // grid-area: Thursday;
    grid-column: 4;
  }
  &.Viernes {
    // grid-area: Friday;
    grid-column: 5;
  }
  &.Sabado {
    // grid-area: Saturday;
    grid-column: 6;
  }
  &.Domingo {
    // grid-area: Sunday;
    grid-column: 7;
  }
}

.legend-container{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  padding: 14px 10px;
  width: 100%;

  .legend{
    display: inline-flex;
    align-items: center;
    font-size: 0.8em;
    line-height: .8em;
    margin: 0;
    &::before{
      content: '';
      display: inline-block;
      width: 10px;
      height: 10px;
      margin-right: 1ch;
      border-radius: 50%;
      background-color: rgb(241, 188, 241);
    }
    &.full::before{
      background-color: $blueLight;
    }
    &.subscribed::before{
      background-color: inherit;
      border: 1px solid $dangerous;
    }
    &.inQueue::before {
      background-color: inherit;
      border: 1px solid $blueDark
    }
    &.current-day::before{
      content: '1';
      color: red;
      background-color: inherit;
      border-radius: 0;
    }
    &:last-of-type{
      margin-right: 0;
    }
  }
}
</style>
