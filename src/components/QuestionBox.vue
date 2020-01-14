<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template v-slot:lead>
                <span v-html="currentQuestion.question"></span>
            </template>

            <hr class="my-4">

            <b-list-group class="answers-container">
                <b-list-group-item
                    v-for="(answer, index) in suffledAnswers" 
                    :key="index"
                    :class="answerClass(index)"
                    @click="selectAnswer(index)"
                >
                    {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button variant="primary"
                @click="submitAnswer"
                :disabled="selectedIndex == null || this.answered"
            >
                Submit
            </b-button>
            <b-button variant="success" 
                @click="next"
                :disabled="!this.answered"
            >
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    components: {
        
    },
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            answered: false,
            suffledAnswers: [],
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null
                this.suffleAnswers()
                this.answered = false

                if (this.indexQuestion == this.totalQuestion - 1) {
                    this.last = true
                }
            }
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
        },
        suffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.suffledAnswers = _.shuffle(answers)
            this.correctIndex = this.suffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer() {
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }

            this.answered = true
            this.increment(isCorrect)
        },
        answerClass(index) {
            let classAnswer = ''

            if (!this.answered && this.selectedIndex == index) {
                classAnswer = "selected"
            }
            else if (this.answered && this.correctIndex == index) {
                classAnswer = "correct"
            }
            else if (this.answered && this.selectedIndex == index && this.correctIndex != index) {
                classAnswer = "incorrect"
            }

            return classAnswer
        }
    }
}
</script>

<style scoped>
    .answers-container {
        margin-bottom: 14px;
    }
    .list-group-item {
        cursor: pointer;
    }
    .list-group-item:hover {
        background-color: #ddd;
    }
    .btn {
        margin: 0 5px;
    }

    .selected {
        background-color: dodgerblue;
        color: #fff
    }
    .correct {
        background-color: rgb(113, 180, 113);
        color: #fff
    }
    .incorrect {
        background-color: crimson;
        color: #fff
    }
</style>