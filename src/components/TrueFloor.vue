<template>
    <div class="true-floor">
        <p class="true-floor__number">
            {{ floor }}
        </p>
        <button
            type="button"
            class="true-floor__button"
            @click="emit('clickButton')"
            ></button>
    </div>
</template>

<script setup>

defineProps({
    floor: {
        type: Number,
        required: true
    },
})

const emit = defineEmits(['clickButton'])

</script>

<style lang="scss" scoped>
.true-floor {
    padding: 0 20px 20px 20px;
    border: 1px solid #402d7d;
    background-color: #cecdd4;

    &__button {
        width: 16px;
        height: 16px;
        border: 1px solid #20163f;
        border-radius: 4px;

        &:hover {
            cursor: pointer;
        }
    }
}
</style>