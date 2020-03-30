<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">
            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in shuffledAnswers" 
                    :key="index"
                    @click.prevent="selectAnswer(index)"
                    :class=answerClass(index)
                > <!--clicking passes index into selectAnswer(index), class runs by default -->
                    {{answer}}
                </b-list-group-item>
            </b-list-group>

            <b-button 
             variant="primary"
             @click="submitAnswer"
             :disabled="selectedIndex === null || answered "
            >
             Submit
            </b-button>
            <b-button @click="next" href="#">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>
 
<script>
import _ from 'lodash'

export default {
    props: {
        //passed the data property from app.vue to questionbox
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
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },
    watch: {
        currentQuestion: { 
            immediate: true, //runs when currentQuestion first gets passed as props and
                             //when current question changes from the parent, handler runs
            handler() {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
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

            this.increment(isCorrect)
            this.answered = true
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            //last part of the above gives 'correct_answer' value and passes it to the shuffled answer list
            // to get index of correct answer 
        },
        answerClass(index) {
            let answerClass = ''

            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if (this.answered &&
            this.selectedIndex === index &&
            this.correctIndex !== index
            ) {
                answerClass = 'incorrect'
            }

            return answerClass
        }
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 15px;
}
.list-group-item:hover {
    background: #EEE;
    cursor: pointer;
}
.btn {
    margin: 0 5px;
}
.selected {
    background-color: lightblue;

}
.correct {
    background-color: lightgreen;

}
.incorrect {
    background-color: red;
}
</style>