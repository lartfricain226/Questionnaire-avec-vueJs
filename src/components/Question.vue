<template>
    <div class="question">
        <h3>
            {{ question.question }}
        </h3>
        <ul>
            <li v-for="(choice, index) in randomChoices" :key="choice">
                <Answer :id="`answer-${index}`" :value="choice" :disabled="hasAnswer" @change="onAnswer"
                v-model="answer" :correctAnswer="question.correct_answer"></Answer>
            </li>
        </ul>
        <button :disabled="!hasAnswer" @click="emits('answer', answer)">Question suivante</button>
    </div>
</template>

<script setup>
import { shuffleArray } from '@/functions/Array';
import { computed, onMounted, onUnmounted, ref } from 'vue';
import Answer from './Answer.vue';


const props = defineProps(
    {
        question: Object
    }
)

const answer = ref(null)
const emits = defineEmits(
    ['answer']
)
const hasAnswer = computed(() => answer.value !== null)

// watch(() => props.question, () => {
//     answer.value = null
// })

const randomChoices = computed(() => shuffleArray(props.question.choices))


let timer 
const onAnswer = (event) => {
    clearTimeout(timer)
    timer = setTimeout(() => {
        emits('answer', answer.value)
    }, 5000)
}

onMounted(() => {
    timer = setTimeout(() => {
        answer.value = ''
        onAnswer()
    }, 15000)
})

onUnmounted(() => {
    clearTimeout(timer)
})
</script>

<style scoped>
.question {
    padding-top: 2rem;
}

.question button {
    margin-right: auto;
    display: block;
}
</style>