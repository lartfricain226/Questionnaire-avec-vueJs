<template>
    <div v-show="state === 'question'">
        <h1>
            {{ quiz.title }}
        </h1>
        <Progress :value="step" :max="quiz.questions.length - 1"></Progress>
        <Question :key="question.question" :question="question" v-if="state === 'question'" @answer="addAnswer"></Question>
    </div>
    <Recap :answers="answers" v-if="state === 'recap'" :quiz="quiz"></Recap>
</template>

<script setup>
import { computed, ref } from 'vue';
import Progress from './Progress.vue';
import Question from './Question.vue';
import Recap from './Recap.vue';

const props = defineProps(
    {
        quiz: Object
    }
)

const step = ref(0)
const question = computed(() => {
    return props.quiz.questions[step.value]
})

const answers = ref(props.quiz.questions.map(() => null))

const state = ref('question')

const addAnswer = (answer) => {
    answers.value[step.value] = answer
    if (step.value === props.quiz.questions.length - 1) {
        state.value = 'recap'
    } else {
        step.value++
    }
}
</script>