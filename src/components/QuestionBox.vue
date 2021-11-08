<template>
  <div class="question-box-container">
    <b-jumbotron>

      <template #lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item v-for="(answer, index) in answers"
                           :key="index"
                           @click="selectAnswer(index)"
                           :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
          variant="primary"
          @click="submitAnswer"
          :disabled="this.selectedIndex === null || this.answered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  name: "QuestionBox",
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      answered: false,
      correctIndex: null,
    }
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false
      if (this.shuffledAnswers[this.selectedIndex] === this.currentQuestion.correct_answer) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
    answerClass(index) {
      let aClass = ''
      if (!this.answered && this.selectedIndex === index) {
        aClass = 'selected'
      } else if (this.answered && this.correctIndex === index) {
        aClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        aClass = 'incorrect'
      }
      return aClass
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  }
}
</script>

<style scoped>
.list-group {
  padding-bottom: 15px;
}

.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background: lightblue;
}

.correct {
  background: lightgreen;
}

.incorrect {
  background: red;
}
</style>
