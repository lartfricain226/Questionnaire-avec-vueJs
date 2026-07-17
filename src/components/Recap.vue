<template>
    <div class="page">
        <div class="card" :class="{ 'is-won': hasWon, 'is-lost': !hasWon }">
            <p class="eyebrow">Résultats</p>
            <h1>Récapitulatif</h1>

            <div class="ring-wrap">
                <svg viewBox="0 0 160 160" class="ring">
                    <circle
                        class="ring-track"
                        cx="80" cy="80" r="70"
                        fill="none" stroke-width="12"
                    />
                    <circle
                        class="ring-progress"
                        cx="80" cy="80" r="70"
                        fill="none" stroke-width="12"
                        stroke-linecap="round"
                        :stroke-dasharray="circumference"
                        :style="{ strokeDashoffset: ready ? offset : circumference }"
                    />
                </svg>
                <div class="ring-center">
                    <span class="ring-score">{{ score }}</span>
                    <span class="ring-total">/ {{ quiz.questions.length }}</span>
                </div>
            </div>

            <p class="verdict">
                {{ hasWon ? quiz.success_message : quiz.failure_message }}
            </p>

            <ul v-if="incorrectAnswers.length" class="breakdown">
                <li
                    v-for="item in incorrectAnswers"
                    :key="item.index"
                >
                    <div class="breakdown-row">
                        <span class="badge">✕</span>
                        <span class="label">
                            {{ quiz.questions[item.index].label ?? quiz.questions[item.index].text ?? quiz.questions[item.index].question ?? ('Question ' + (item.index + 1)) }}
                        </span>
                    </div>
                    <div class="correction">
                        <p class="correction-line">
                            <span class="correction-tag">Votre réponse</span>
                            <span class="wrong-answer">{{ item.answer }}</span>
                        </p>
                        <p class="correction-line">
                            <span class="correction-tag">Bonne réponse</span>
                            <span class="right-answer">{{ quiz.questions[item.index].correct_answer }}</span>
                        </p>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue'

const props = defineProps({
    answers: Array,
    quiz: Object
})

const score = computed(() => {
    return props.answers.reduce((total, answer, index) => {
        if (answer === props.quiz.questions[index].correct_answer) {
            return total + 1
        }
        return total
    }, 0)
})

const hasWon = computed(() => {
    return score.value >= props.quiz.minimum_score
})

const isCorrect = (index) => {
    return props.answers[index] === props.quiz.questions[index].correct_answer
}

const incorrectAnswers = computed(() => {
    return props.answers
        .map((answer, index) => ({ answer, index }))
        .filter((item) => !isCorrect(item.index))
})

const percentage = computed(() => {
    return props.quiz.questions.length
        ? score.value / props.quiz.questions.length
        : 0
})

const radius = 70
const circumference = 2 * Math.PI * radius
const offset = computed(() => circumference - percentage.value * circumference)

// déclenche l'animation de remplissage une fois le composant monté
const ready = ref(false)
onMounted(() => {
    requestAnimationFrame(() => {
        ready.value = true
    })
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fraunces:wght@500;600&family=Inter:wght@400;500;600&display=swap');

.page {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 48px 24px;
    background:
        radial-gradient(circle at 50% -10%, #2a2440 0%, #14121f 60%),
        #14121f;
    font-family: 'Inter', sans-serif;
    color: #f5f3ff;
}

.card {
    width: 100%;
    max-width: 680px;
    background: #1e1b2e;
    border-radius: 32px;
    padding: 56px 56px 48px;
    text-align: center;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.45);
    border: 1px solid rgba(255, 255, 255, 0.06);
    transition: box-shadow 0.4s ease;
}

.card.is-won {
    box-shadow: 0 20px 60px rgba(125, 216, 160, 0.15), 0 0 0 1px rgba(125, 216, 160, 0.15);
}

.card.is-lost {
    box-shadow: 0 20px 60px rgba(255, 107, 107, 0.12), 0 0 0 1px rgba(255, 107, 107, 0.12);
}

.eyebrow {
    margin: 0 0 6px;
    font-size: 14px;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    color: #a8a3bd;
    font-weight: 600;
}

h1 {
    margin: 0 0 36px;
    font-family: 'Fraunces', serif;
    font-weight: 600;
    font-size: 42px;
    letter-spacing: -0.01em;
}

.ring-wrap {
    position: relative;
    width: 200px;
    height: 200px;
    margin: 0 auto 32px;
}

.ring {
    width: 100%;
    height: 100%;
    transform: rotate(-90deg);
}

.ring-track {
    stroke: #2a2640;
}

.ring-progress {
    stroke: v-bind('hasWon ? "#7dd8a0" : "#ff6b6b"');
    transition: stroke-dashoffset 1.1s cubic-bezier(0.22, 1, 0.36, 1);
}

.ring-center {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.ring-score {
    font-family: 'Fraunces', serif;
    font-size: 52px;
    font-weight: 600;
    line-height: 1;
}

.ring-total {
    font-size: 17px;
    color: #a8a3bd;
    margin-top: 4px;
}

.verdict {
    font-size: 19px;
    line-height: 1.5;
    color: #f5f3ff;
    background: #262238;
    border-radius: 18px;
    padding: 20px 24px;
    margin: 0 0 32px;
}

.breakdown {
    list-style: none;
    margin: 0;
    padding: 0;
    text-align: left;
}

.breakdown li {
    list-style: none;
    padding: 20px 4px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.06);
}

.breakdown li:last-child {
    border-bottom: none;
}

.breakdown-row {
    display: flex;
    align-items: center;
    gap: 12px;
}

.badge {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: 26px;
    height: 26px;
    border-radius: 50%;
    font-size: 13px;
    font-weight: 700;
    flex-shrink: 0;
    background: rgba(255, 107, 107, 0.15);
    color: #ff6b6b;
}

.label {
    color: #e4e1f2;
    font-weight: 500;
    font-size: 16px;
}

.correction {
    margin: 14px 0 2px 38px;
    padding-top: 14px;
    border-top: 1px dashed rgba(255, 255, 255, 0.08);
    display: flex;
    flex-direction: column;
    gap: 6px;
}

.correction-line {
    margin: 0;
    font-size: 15px;
    display: flex;
    gap: 8px;
    align-items: baseline;
}

.correction-tag {
    color: #a8a3bd;
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    flex-shrink: 0;
    min-width: 104px;
}

.wrong-answer {
    color: #ff6b6b;
    text-decoration: line-through;
    text-decoration-color: rgba(255, 107, 107, 0.5);
}

.right-answer {
    color: #7dd8a0;
    font-weight: 600;
}

@media (prefers-reduced-motion: reduce) {
    .ring-progress {
        transition: none;
    }
}
</style>