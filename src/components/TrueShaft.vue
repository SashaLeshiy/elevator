<template>
    <div class="true-shaft">
        <div 
            class="true-shaft__elevator"
            :style="style"
        >
            {{ props.direction }}
            {{ props.target }}
            {{ props.currentFloor }}
        </div>
    </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
    direction: String,
    target: Number,
    currentFloor: Number
})

const style = computed(() => {
    switch(props.currentFloor) {
        case 2:
            return 'bottom: 90px'
        case 3:
            return 'bottom: 180px'
        case 4:
            return 'bottom: 270px'
        case 5:
            return 'bottom: 360px'
        default:
            return 'bottom: 0'
    }
})

</script>

<style lang="scss" scoped>
.true-shaft {
    position: relative;
    width: 50px;
    height: 100%;
    border: 1px solid #402d7d;

    &__elevator {
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 90px;
        background-color: #8c8897;
        transition: all 1s ease;
    }
}
</style>