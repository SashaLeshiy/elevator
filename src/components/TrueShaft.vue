<template>
    <div class="true-shaft">
        <div 
            :class="classes"
            :style="style"
        >
            <p class="true-shaft__elevator-target">
                {{ props.target }}
            </p>
            <p class="true-shaft__elevator-direction">
                {{ iconDirection }}
            </p>
        </div>
    </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
    direction: String,
    target: Number,
    currentFloor: Number,
    wait: Boolean,
    park: Number
})

const style = computed(() => {
    if(props.currentFloor === 1) {
        return 'bottom: 0'
    } else if (props.target === 0) {
        return `bottom: ${(props.currentFloor - 1) * 90}px`
    } else {
        return [
        `bottom: ${(props.target - 1) * 90}px`,
        `transition: all ${Math.abs(props.park - props.target)}s`
        ]
    }
        
})

const classes = computed(() => {
    return [
        'true-shaft__elevator',
        props.wait && 'true-shaft__elevator-active'
    ]
})

const iconDirection = computed(() => {
    if(props.direction === 'UP') {
        return '👆'
    } else {
        return '👇'
    }
})
</script>

<style lang="scss" scoped>
.true-shaft {
    position: relative;
    box-sizing: border-box;
    width: 50px;
    height: 100%;
    border: 1px solid #402d7d;

    &__elevator {
        position: absolute;
        left: 0;
        bottom: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-around;
        width: 100%;
        height: 90px;
        background-color: #8c8897;
        transition: all 1s ease-in-out;
    }

    &__elevator-active {
        background-color: inherit;
    }

    &__elevator-target {
        margin: 0;
    }

    &__elevator-wait {
        margin: 0;
    }

    &__elevator-direction {
        margin: 0;
    }
}
</style>