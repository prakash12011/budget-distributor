<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import { ref } from 'vue'

const amount = ref(0);
const notification = ref(0);
const editMode = ref(0);
const percAmount = ref(0);
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
  percentageArr.value.push({
    name: '',
    val: ''
  });
}

const saveData = () => {
  const data = percentageArr.value
  localStorage.setItem('percentage', JSON.stringify(data));
  percentage.value = JSON.stringify(data);
  percentageArr.value = JSON.parse(percentage.value);
}

const convertData = () => {
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

const getPercentageSum = () => {
  var sum = 0;
  percentageArr.value.forEach(el => sum += el.val)
  return sum
}

const showNotification = (mes, time = 2000) => {
  notification.value = mes
  setTimeout(() => notification.value = '', time);
}

const editPercentage = () => {
  percentage.value = ''
  percAmount.value = []
  editMode.value = true
}
</script>

<template>
  <div v-if="notification" class="notification">
    <span v-html="notification"></span>
  </div>

  <div v-if="percentage" class="amount-container">
    <h2>Enter Amount </h2>
    <input type="number" name="amount" id="amount" v-model="amount" />
    <button v-show="percentageArr.length > 0" @click="convertData">Convert into percentage</button>
    <button v-show="percentageArr.length > 0" @click="editPercentage">Edit percentage</button>
  </div>
  
  <div class="percentage-setup" v-if="!percentage && !editMode">
    <button @click="addItem">Add new</button>
    <div class="percentage-item" v-for="(item, itemIndex) in percentageArr" :key="itemIndex">
      <p>Enter percentage label <input type="text" v-model="item.name"></p>
      <p>Enter percentage <input type="number" v-model="item.val"></p>
    </div>
    <button v-show="percentageArr.length > 0" @click="saveData">Save Data</button>
  </div>

  <div class="percentage-setup" v-if="!percentage && editMode">
    <button @click="addItem">Add new</button>
    <div class="percentage-item" v-for="(item, itemIndex) in percentageArr" :key="itemIndex">
      <p>Enter percentage label <input type="text" v-model="item.name"></p>
      <p>Enter percentage <input type="number" v-model="item.val"></p>
    </div>
    <button v-show="percentageArr.length > 0" @click="saveData">Save Data</button>
  </div>
<!-- 
  <div class="percentage-val" v-for="(item, itemIndex) in percentageArr" :key="itemIndex">
  <div class="percentage-val" v-for="(item, itemIndex) in percentageArr" :key="itemIndex">
    {{`${item.name}: ${item.val}`}}
  </div> -->

  <div class="percentage-val" v-for="(perc, percIndex) in percAmount" :key="percIndex">
    {{`${perc.name}: ${perc.val}`}}
  </div>
</template>

<style>
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
