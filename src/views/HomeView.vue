<script setup>
import { ref, reactive, computed, onMounted } from 'vue'
import TrueFloor from '../components/TrueFloor.vue'
import TrueShaft from '../components/TrueShaft.vue'

const elevators = reactive([{ id: 1, floor: 1 }])
const floors = ref([1, 2, 3, 4, 5])
const callButton = ref(1)
const calls = ref([])
const direction = ref()
const wait = ref(false)

const call = (index) => {
  callButton.value = index + 1
  if (calls.value.length > 0 && calls.value.find((e) => e === callButton.value)) {
    return null
  } else if (elevators[0].floor === callButton.value) {
    return null
  } else {
    calls.value = [...calls.value, callButton.value]
    localStorage.setItem('calls', JSON.stringify(calls.value))
    if (calls.value.length <= 1) {
      move()
    }
  }
}

const second = () => {
  return new Promise((resolve) => setTimeout(resolve, 1000))
}

const up = () => {
  elevators[0].floor++
  localStorage.setItem('floor', JSON.stringify(elevators[0].floor))
}

const down = () => {
  elevators[0].floor--
  localStorage.setItem('floor', JSON.stringify(elevators[0].floor))
}

const move = async () => {
  while (elevators[0].floor !== calls.value[0]) {
    if (elevators[0].floor < calls.value[0]) {
      direction.value = 'UP'
      up()
      await second()
    } else {
      direction.value = 'DOWN'
      down()
      await second()
    }
  }
  wait.value = true
  await second()
  wait.value = false
  await second()
  wait.value = true
  await second()
  wait.value = false

  calls.value.splice(0, 1)
  localStorage.setItem('calls', JSON.stringify(calls.value))

  if (calls.value[0]) {
    move()
  }
  wait.value = false
}

const targetFloor = computed(() => {
  if (calls.value[0]) {
    return calls.value[0]
  } else {
    return elevators[0].floor
  }
})

const activeButton = (floor) => {
  if(calls.value.length > 0 && calls.value.find((e) => e === floor)) {
    return true
  } else {
    return false
  }
}

onMounted(() => {
  let dataFloor = JSON.parse(localStorage.getItem('floor'))
  let dataCalls = JSON.parse(localStorage.getItem('calls'))

  if (dataFloor) {
    elevators[0].floor = JSON.parse(localStorage.getItem('floor'))
  }
  if (dataCalls && dataCalls.length > 0) {
    calls.value = JSON.parse(localStorage.getItem('calls'))
    console.log(calls.value)
    move()
  }
})

</script>

<template>
  <main class="home-view">
    <TrueShaft
      v-for="shaft in elevators"
      :key="shaft.id"
      :direction="direction"
      :target="targetFloor"
      :current-floor="elevators[0].floor"
      :wait="wait"
    />
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
  <div class="home-view__floor">
    <p>лифт {{ elevators[0].id }} на этаже - {{ elevators[0].floor }}</p>
    <p>стек вызовов - {{ calls }}</p>
  </div>
</template>

<style lang="scss" scoped>
.home-view {
  display: grid;
  grid-template-columns: max-content 1fr;
}
</style>
