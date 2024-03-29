<template>
  <div class='yearHeader'>
    <span :class="['prev', 'year', { disabled: currYear <= minYear } ]" @click=" currYear -= 1 ">&lt;</span>
    <h3 class='year'>{{ currYear }}</h3>
    <span :class="['next', 'year', { disabled: currYear >= maxYear } ]" @click=" currYear += 1 ">&gt;</span>
  </div>
  <p v-if="!payments().length" class='no-data'>¡No hay historial de pagos en este período!</p>
  <div v-else class='payments-calendar'>
    <template v-for="element in payments()" :key="element">
      <div class='month' v-if="element.payments.length">
        <h5 class='name'>{{ getMonthNameByNumber(element.monthYear) }}</h5>
        <div :class="['payment', {paid: payment.isPaid !== 'no'}]" v-for="payment in element.payments" :key="payment">
          <p class='type'>{{ payment.type }}:</p>
          <p class='qty'>{{ payment.qty }}</p>
        </div>
      </div>
    </template>
  </div>
</template>

<script setup>
import { useStore } from 'vuex'
import { ref } from 'vue'
import { getMonthNameByNumber } from '@/services/util/time.js'

const store = useStore()

const currYear = ref(new Date().getFullYear())
const maxYear = new Date().getFullYear() + 2
const minYear = new Date().getFullYear() - 5
const userData = store.getters.getUser

function payments() {
  if (!userData || !userData.payments) return []

  const list = userData.payments
  return list.filter(p => {
    const paymentYear = new Date(p.monthYear).getFullYear()
    if (paymentYear === currYear.value) return p
  })
}
</script>

<style lang="scss" scoped>
@import '@/colors.scss';
.yearHeader {
  display: flex;
  justify-content: space-around;
  align-items: center;
  .year { margin: 0; }

  .prev,
  .next {
    font-size: 2em;
    padding: 0 1em;
    cursor: pointer;
    &.disabled {
      color: rgba($blueDark, .4);
      pointer-events: none;
    }
  }
}
.no-data {
  text-align: center;
}
.payments-calendar {
  display: grid;
  grid-template-columns: repeat(2, 150px);
  grid-column-gap: 4px;
  grid-row-gap: 4px;
  margin: 0 auto;
}
.month {
  padding: 8px;
  border: 1px solid rgba($blueDark, .4);
  border-radius: 4px;
  min-width: 140px;
  background-color: $white;
  .name {
    text-align: center;
    margin: 0.1em 0 .3em;
  }
}
.payment {
  display: flex;
  align-items: baseline;
  // height: 1.5rem;
  p {
    margin: 0;
  }

  &::before {
    content: '\2717';
    width: 1ch;
    margin-right: 1ch;
    color: $dangerous;
  }
  &.paid {
    &::before {
      content: '\2713';
      color: $valid;
    }
    .type,
    .qty {
    text-decoration: line-through;
    }
  }
  .type,
  .qty {
    display: inline-block;
  }
  .type {
    margin-right: 1ch;
  }
  .qty {
    &::after{
      content: '€';
      width: 1ch;
    }
  }
}

@media (min-width: 600px) {
.payments-calendar {
  grid-template-columns: repeat(3, 150px);
}
}
</style>
