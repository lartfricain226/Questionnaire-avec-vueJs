<template>
  <div class="container">
    <div v-if="state === 'error'">Une erreur est survenue</div>
    <div :aria-busy="state === 'loading'">
      <Quiz v-if="quiz" :quiz="quiz"></Quiz>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import Quiz from './components/Quiz.vue';

const quiz = ref(null)
const state = ref("loading")

onMounted(() => {
  fetch(`${import.meta.env.BASE_URL}quiz.json`)
    .then(res => {
      if (res.ok) {
        return res.json()
      }
      throw new Error('Une erreur est survenue lors de l\'importation du questionnaire')
    })
    .then(json => {
      quiz.value = json
      state.value = "loaded"
    })
    .catch(err => {
      state.value = "error"
    })
})
</script>

<style scoped>
.container {
  margin-top: 2rem;
}
</style>
