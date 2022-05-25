<script setup>
import PieChart from './components/PieChart.vue'
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import { ref } from 'vue'

function createDebounce() {
  let initTimeout;

  return function (fn, delay) {
    clearTimeout(initTimeout);
    initTimeout = setTimeout(() => {
      fn();
    }, delay || 300);
  }
}

const amount = ref(0);
const notification = ref({});
const editMode = ref(0);
const percAmount = ref(0);
const totalPercAmount = ref(0);
const debounce = createDebounce();

percAmount.value = [];

const percentageArr = ref(0);
percentageArr.value = [];

const percentage = ref(0);
percentage.value = localStorage.getItem('percentage');
if( percentage.value ) {
  percentageArr.value = JSON.parse(percentage.value);
}

const addItem = () => {
  if(getPercentageSum() >= 100) {
    showNotification(`You can't add values more then 100%`);
    return
  }
  editMode.value = true
  percentageArr.value.push({
    name: '',
    val: ''
  });
}

const saveData = () => {
  if(getPercentageSum() > 100) {
    showNotification(`You can't add values more then 100%`, 'error');
    return
  }
  const data = percentageArr.value
  localStorage.setItem('percentage', JSON.stringify(data));
  percentage.value = JSON.stringify(data);
  percentageArr.value = JSON.parse(percentage.value);
  editMode.value = false
}

const convertData = () => {
  if ( !amount.value ) {
    showNotification('Enter a valid Number');
    return
  }
  var sum = 0
  percentageArr.value.forEach(el => {
    let val = (amount.value / 100) * el.val
    sum += val
    percAmount.value.push({
      name: el.name,
      val: val
    })
  });

  if( sum < amount.value ) {
    percAmount.value.push({
      name: 'Unassigned',
      val: amount.value - sum
    })
  }
}

const getPercentageSum = () => percentageArr.value.reduce((acc, curr) => acc + curr.val, 0);

const showNotification = (mes, type = 'info', time = 2000) => {
  notification.value.message = mes
  notification.value.type = type
  setTimeout(() => notification.value.message = '', time);
}

const editPercentage = () => {
  totalPercAmount.value = getPercentageSum()
  percentage.value = ''
  percAmount.value = []
  editMode.value = true
}

const addToTotal = (val) => {
  if (!val) return
  debounce( () => totalPercAmount.value = getPercentageSum(), 100);
}
</script>

<template>
  <div v-if="notification.message" class="notification">
    <span v-html="notification.message"
      :style="`color: ${notification.type == 'error' ? 'red' : ''}`"
    ></span>
  </div>

  <div v-if="percentage" class="amount-container">
    <h2>Enter Amount </h2>
    <input type="number" name="amount" id="amount" v-model="amount" />
    <button v-show="percentageArr.length > 0" @click="convertData">Convert into percentage</button>
    <button v-show="percentageArr.length > 0" @click="editPercentage">Edit percentage</button>
  </div>
  
  <div class="percentage-setup" v-if="!percentage">
    <button @click="addItem">Add new</button> 
    <span v-if="editMode" 
      :style="`color: ${totalPercAmount > 100 ? 'red' : ''}`"
    >Total Percentage {{totalPercAmount}}</span>
    <div class="percentage-item" v-for="(item, itemIndex) in percentageArr" :key="itemIndex">
      <p>Enter percentage label <input type="text" v-model="item.name"></p>
      <p>Enter percentage <input type="number" @input="addToTotal(item.val)" v-model="item.val"></p>
    </div>
    <button v-show="percentageArr.length > 0" @click="saveData">Save Data</button>
  </div>
  
  <div class="percentage-val" v-for="(perc, percIndex) in percAmount" :key="percIndex">
    {{`${perc.name}: ${perc.val}`}}
  </div>

  <div style="max-width: 30%; margin: 50px auto 0" v-if="(amount && percAmount.length > 0) && !editMode">
    <PieChart :data="percAmount"/>
  </div>
</template>

<style lang="scss">
@import 'src/assets/scss/style';
</style>
