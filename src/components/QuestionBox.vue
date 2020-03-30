<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template v-slot:lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">
            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in answers" 
                    :key="index"
                    @click.prevent="selectAnswer(index)"
                    :class="[selectedIndex === index ? 'selected' : '']"
                > <!--clicking passes index into selectAnswer(index), class runs by default -->
                    {{answer}}
                </b-list-group-item>
            </b-list-group>

            <b-button 
             variant="primary"
             @click="submitAnswer"
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
            shuffledAnswers: []
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
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            //last part of the above gives 'correct_answer' value and passes it to the shuffled answer list
            // to get index of correct answer 
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