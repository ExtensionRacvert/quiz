
<template>
    <div class="question-box-container bg-light p-3 mt-4">
        <b-jumbotron>
            <template #lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                          class="list-group-item mb-2" 
                          v-for="(answer, index) in answers" 
                          :key="index" 
                          @click="selectAnswer(index)"
                          :class="answerClass(index)"
                >
                {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button variant="primary" 
                      class="btn"
                      @click="submitAnswer"
                      :disabled="selectedIndex === null || answered"
            >
                Submit
            </b-button>
            <b-button variant="success" class="btn" @click="next">Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
        // eslint-disable-next-line vue/return-in-computed-property
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null
                this.shuffleAnswers()
                this.answered = false
            }
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
        },
        submitAnswer() {
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerClass(index) {
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                answerClass = 'incorrect'
            }

            return answerClass
        }
    }
}
</script>

<style scoped>
.btn {
    margin: 0 5px;
}
.list-group-item {
    cursor: pointer;
}
.list-group-item:hover {
    background-color: rgb(236, 235, 235);
}
.selected {
    background-color: rgb(142, 142, 236);
    color: white;
}
.correct {
    background-color: green;
    color: white;
}
.incorrect {
    background-color: rgb(161, 58, 58);
    color: white;
}
</style>
