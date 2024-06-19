<template>
<div>
  <h1>Today is a new day</h1>
  <form id="time-form">
    <label for="start-time">Start of the day : </label>
    <input id="start-time" type="time" name="start-time" value="00:00"> 
    <button @click.prevent="validateForm()">OK</button>
    <br>

    <label for="select-colors">Select a colour : </label>
    <select name="select-colors" id="select-colors">
      <option value="red">Red</option>
      <option value="orange">Orange</option>
      <option value="yellow">Yellow</option>
      <option value="green">Green</option>
      <option value="blue">Blue</option>
      <option value="purple">Purple</option>
      <option value="pink">Pink</option>
    </select>
  </form>

  <div>
    <div class="hours-container">
      <button v-for="(hours, index) in hoursArray" :key="index" :id="index"
      :class="[index%2 ===0 ? 'big-block' : 'small-block',
      currentClass,
      checkTimeOfDay(hours) ? 'currentTime' : '']"
      @click="submitButtonStyle(index)"
      >
        <!-- {{ hours }} -->
        {{ index%2 ===0 ? hours.slice(0,2) : hours.slice(3,5) }}
      </button>
    </div>
  </div>

</div>
</template>

<script setup>
import { ref,reactive } from 'vue'
var currentDate = new Date()
var currentHour = currentDate.getHours()
var currentMins = currentDate.getMinutes()

var hoursArray = reactive([]);

if(JSON.parse(localStorage.getItem('localHoursArray')) === null){
  for (var i = 0; i <= 47; i++) {
      var n = i%2==0 ? i/2+':00' : (i+1)/2-1+':30'
      if(i<20){
        n = '0'+n
        } //zero-left padding
      hoursArray.push(n)
  }
  localStorage.setItem('localHoursArray',JSON.stringify(hoursArray))
} else {
  hoursArray = JSON.parse(localStorage.getItem('localHoursArray'))
}

const currentClass = reactive({
  'active': true,
  'rotate': false,
})

function checkTimeOfDay(time){
  let hours = time.slice(0,2)
  let mins = time.slice(3,5)
  if (currentHour == hours){
    if (mins == 0){
      if (currentMins < 30) {
        return true
      } else {
        return false
      }
    } else {
      if (currentMins >= 30) {
        return true
      }
      else{
        return false
      }
    }
  } else {
    return false
  }
}

function submitButtonStyle(id) { 
  let selectedColor = document.getElementById("select-colors").value.toString()
  let currentStyle = document.getElementById(id).style.backgroundColor;

  document.getElementById(id).style.backgroundColor = document.getElementById("select-colors").value.toString()
  if (currentStyle == selectedColor){
    document.getElementById(id).style.backgroundColor = "gray"
  }
}

function shiftArray(arr, direction, n) {
    var times = n > arr.length ? n % arr.length : n;
    return arr.concat(arr.splice(0, (direction > 0 ? arr.length - times : times)))
}

function validateForm(){
  let localHoursArray = JSON.parse(localStorage.getItem('localHoursArray'))
  console.log(localHoursArray)
  if (localHoursArray=== null){
      localStorage.setItem('localHoursArray',JSON.stringify(hoursArray))
      console.log(localHoursArray)
    } else if (localHoursArray !== hoursArray){
    let startTime = document.getElementById("time-form").elements["start-time"].value.toString()
    let startIndex = 0;
    
    for (let index = 0; index < localHoursArray.length; index++) {
      if (startTime === localHoursArray[index]) {
        startIndex = index
      }
    }
    hoursArray = shiftArray(hoursArray,0,startIndex)
    localStorage.setItem('localHoursArray',JSON.stringify(hoursArray))
  }
  window.location.reload()
}

</script>

<style scoped>
.container{
  width: 80%;
  margin: 0 auto;
}

.hours-container{
    margin: 0;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    height: 100px;
    gap: 3px;
    align-items: end;
    width: 100vw;
    /* -webkit-text-stroke: 1px white; */
}

.active{
    background-color: gray;
    margin : 0;
    margin-bottom: 5px;
    padding : 5px;
    border-radius: 5px;
    align-items: end;
    color: rgb(54, 23, 59);
}

.small-block{
  height: 50px;
  display: inline-flex;
  align-items: flex-end;
}

.big-block{
  height: 70px;
  display: inline-flex;
  align-items: flex-start;
  font-size: 0.9em;
}

.currentTime{
  outline: 2px dashed orangered!important ;
}

</style>