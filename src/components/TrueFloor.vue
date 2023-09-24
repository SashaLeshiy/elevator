<template>
    <div class="true-floor">
        <p class="true-floor__number">
            {{ props.floor }}
        </p>
        <button
            type="button"
            :class="classes"
            @click="emit('clickButton')"
        >☺</button>
    </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
    floor: {
        type: Number,
        required: true
    },
    activeButton: {
        type: Boolean
    }
})

const emit = defineEmits(['clickButton'])

const classes = computed(() => {
    return [
        'true-floor__button',
        props.activeButton && 'true-floor__button-active'
    ]
})

</script>

<style lang="scss" scoped>
.true-floor {
    padding: 0 20px 20px 20px;
    border: 1px solid #402d7d;
    background-color: #cecdd4;

    &__button {
        display: flex;
        align-items: center;
        justify-content: center;
        padding-bottom: 5px;
        width: 18px;
        height: 18px;
        border: 1px solid #20163f;
        border-radius: 4px;

        &:hover {
            cursor: pointer;
        }
    }

    &__button-active {
        background-color: #cf5d5d;
    }
}
</style>