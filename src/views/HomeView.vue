<script setup>
import { ref, reactive, computed } from 'vue'
import TrueFloor from '../components/TrueFloor.vue'
import TrueShaft from '../components/TrueShaft.vue'

const elevators = reactive([{ id: 1, floor: 1 }])
const floors = ref([1, 2, 3, 4, 5])
const callButton = ref(1)
const calls = ref([])
const direction = ref()

const call = (index) => {
  callButton.value = index + 1
  calls.value = [...calls.value, callButton.value]
  if (calls.value.length <= 1) {
    move()
  }
}

const second = () => {
  return new Promise((resolve) => setTimeout(resolve, 1000))
}

const threeSecond = () => {
  return new Promise((resolve) => setTimeout(resolve, 3000))
}

const up = () => {
  elevators[0].floor++
}

const down = () => {
  elevators[0].floor--
}

const move = async () => {
  while (elevators[0].floor !== calls.value[0]) {
    if (elevators[0].floor < calls.value[0]) {
      direction.value = 'UP'
      await second()
      up()
    } else {
      direction.value = 'DOWN'
      await second()
      down()
    }
  }
  await threeSecond()
  calls.value.splice(0, 1)

  if (calls.value[0]) {
    move()
  }
}

const targetFloor = computed(() => {
  if (calls.value[0]) {
    return calls.value[0]
  } else {
    return elevators[0].floor
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
    />
    <div>
      <TrueFloor
        v-for="floor in floors.slice().reverse()"
        :key="floor"
        :floor="floor"
        @click-button="call(floor - 1)"
      />
    </div>
  </main>
  <div class="home-view__floor">
    <p>Направление - {{ direction }}</p>
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
