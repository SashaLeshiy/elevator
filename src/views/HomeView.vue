<script setup>
import { ref, reactive, onMounted } from 'vue'
import TrueFloor from '../components/TrueFloor.vue'
import TrueShaft from '../components/TrueShaft.vue'
import { FLOORS, ELEVATORS } from '../components/config/config.js'


let dataFloor = JSON.parse(localStorage.getItem('elevators'))
let dataCalls = JSON.parse(localStorage.getItem('calls'))

if(dataFloor && dataFloor.length !== ELEVATORS.length) {
  localStorage.removeItem('elevators')
  localStorage.removeItem('calls')
}

const elevators = reactive(dataFloor || ELEVATORS)

const floors = ref(FLOORS)
const callButton = ref(1)
const calls = ref([])
let copyElevators = ref()

const call = (index) => {
  callButton.value = index + 1
  if (calls.value.length > 0 && calls.value.find((e) => e === callButton.value)) {
    return null
  } else if (elevators.find(e => e.target === callButton.value) || elevators.find(e => e.park === callButton.value)) {
    return null
  } else {
    calls.value = [...calls.value, callButton.value]
    localStorage.setItem('calls', JSON.stringify(calls.value))
    if (calls.value.length <= 1) {
      if(!copyElevators.value) {
        copyElevators.value = [...elevators]
      }
      copyElevators.value.sort((a, b) => Math.abs(callButton.value - a.floor) - Math.abs(callButton.value - b.floor))
      move(copyElevators.value[0].id)
    }
  }
}

const second = () => {
  return new Promise((resolve) => setTimeout(resolve, 1000))
}

const up = (index) => {
  elevators[index].floor++
  localStorage.setItem('elevators', JSON.stringify(elevators))
}

const down = (index) => {
  elevators[index].floor--
  localStorage.setItem('elevators', JSON.stringify(elevators))
}

const move = async (index) => {
  index = index - 1
  elevators[index].target = calls.value[0]
  elevators[index].park = 0

  while (elevators[index].floor !== calls.value[0]) {
    if (elevators[index].floor < calls.value[0]) {
      elevators[index].direction = 'UP'
      up(index)
      await second()
    } else {
      elevators[index].direction = 'DOWN'
      down(index)
      await second()
    }
  }
  elevators[index].park = elevators[index].floor
  elevators[index].wait = true
  await second()
  elevators[index].wait = false
  await second()
  elevators[index].wait = true
  await second()
  elevators[index].wait = false

  calls.value.splice(0, 1)
  localStorage.setItem('calls', JSON.stringify(calls.value))

  if (calls.value[0]) {
    copyElevators.value.sort((a, b) => Math.abs(calls.value[0] - a.floor) - Math.abs(calls.value[0] - b.floor))
    move(copyElevators.value[0].id)
  } else {
    elevators[index].target = 0
  }

  elevators[index].wait = false
  
}

const activeButton = (floor) => {
  if(calls.value.length > 0 && calls.value.find((e) => e === floor)) {
    return true
  } else if(elevators.find(e => e.park === floor)) {
    return true
  } else {
    return false
  }
}

onMounted(() => {
  if (dataFloor) {
    elevators.value = [...dataFloor]
  }
  if (dataCalls && dataCalls.length > 0) {
    calls.value = JSON.parse(localStorage.getItem('calls'))
    
    if(!copyElevators.value) {
      copyElevators.value = [...elevators]
    }
    copyElevators.value.sort((a, b) => Math.abs(calls.value[0] - a.floor) - Math.abs(calls.value[0] - b.floor))

    move(copyElevators.value[0].id)
  }
})


</script>

<template>
  <main class="home-view">
    <div class="home-view__shafts">
      <TrueShaft
        v-for="elevator in elevators"
        :key="elevator.id"
        :direction="elevator.direction"
        :target="elevator.target"
        :current-floor="elevator.floor"
        :wait="elevator.wait"
      />
    </div>
    <div>
      <TrueFloor
        v-for="floor in floors.slice().reverse()"
        :key="floor"
        :floor="floor"
        :active-button="activeButton(floor)"
        @click-button="call(floor - 1)"
      />
    </div>
  </main>
</template>

<style lang="scss" scoped>
.home-view {
  display: grid;
  grid-template-columns: max-content 1fr;

  &__shafts {
    display: flex;
  }
}
</style>
