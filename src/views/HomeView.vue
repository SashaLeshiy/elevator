<script setup>
import { ref, reactive } from 'vue'

const elevators = reactive([{ id: 1, floor: 1 }])
const floors = ref([1, 2, 3, 4, 5])
const callButton = ref(1)
const calls = ref([])
const direction = ref()

const call = (index) => {
  callButton.value = index + 1
  calls.value = [...calls.value, callButton.value]
  if(calls.value.length <= 1) {
    move()
  }
  
}

const second = () => {
  return new Promise(resolve => setTimeout(resolve, 1000))
}

const threeSecond = () => {
  return new Promise(resolve => setTimeout(resolve, 3000))
}

const up = () => {
  elevators[0].floor++
}

const down = () => {
  elevators[0].floor--
}


const move = async () => {
    while(elevators[0].floor !== calls.value[0]) {
    if(elevators[0].floor < calls.value[0]) {
      direction.value = 'UP'
      await second()
      up()
    } else {
      direction.value = 'DOWN'
      await second()
      down()
    }
  }
  console.log('лифт прибыл на этаж - ', elevators[0].floor)
  await threeSecond()
  calls.value.splice(0, 1)

  if(calls.value[0]) {
    move()
  }
}
</script>

<template>
  <main class="home-view">
    <button type="button" v-for="(floor, index) in floors" :key="floor" @click="call(index)">
      {{ floor }}
    </button>
    <div class="home-view__floor">
      <p>Направление - {{ direction }}</p>
      <p>лифт {{ elevators[0].id }} на этаже - {{ elevators[0].floor }}</p>
      <p>стек вызовов - {{ calls }}</p>
    </div>
  </main>
</template>

<style lang="scss" scoped></style>
