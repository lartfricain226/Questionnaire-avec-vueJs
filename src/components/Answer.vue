<template>
    <label :for="id" :class="classes">
        <input type="radio" name="answer" :id="id" :value="value" v-model="model" :disabled="disabled" @change="onChange">
        {{ value }}
    </label>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
    id: String,
    value: String,
    disabled: Boolean,
    correctAnswer: String,
})
const model = defineModel()
const emits = defineEmits(['change'])
const onChange = (event) => {
    emits('change', event)
}

const classes = computed(() => {
    return {
        disabled: props.disabled,
        justAnswered: props.disabled && props.value === props.correctAnswer,
        wrongAnswered: props.disabled && props.value !== props.correctAnswer && model.value === props.value
    }
})
</script>

<style scoped>
.disabled {
    opacity: .5;
}
.justAnswered {
    opacity: 1;
    color: green;
}

.wrongAnswered {
    opacity: 1;
    color: red;
}
</style>