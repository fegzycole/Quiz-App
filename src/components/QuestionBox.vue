<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ question.question }}
      </template>

      <hr class="my-4" />

      <b-list-group class="mb-4">
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button variant="primary"
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered">
        Submit
      </b-button>
      <b-button variant="success" class="ml-2" @click="next"
        >Next</b-button
      >
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    question: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [...this.question.incorrect_answers, this.question.correct_answer];
      this.shuffledAnswers = _.shuffle(answers);
      this.answered = false;
      this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer);
    },
    submitAnswer() {
      let isCorrect = false;

      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    answerClass(index) {
      let answerClass = '';
      if (!this.answered && this.selectedIndex === index) {
        answerClass = 'selected';
      } else if (this.answered && index === this.correctIndex) {
        answerClass = 'correct';
      } else if (this.answered && this.selectedIndex === index && index !== this.correctIndex) {
        answerClass = 'incorrect';
      } else {
        answerClass = '';
      }

      return answerClass;
    }
  },
  watch: {
    question: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
      }
    },
  },
  computed: {
    answers() {
      let answers = [...this.question.incorrect_answers];
      answers.push(this.question.correct_answer);
      return answers;
    }
  }
};
</script>

<style>
  .list-group-item:hover {
    cursor: pointer;
    background: #eee;
  }

  .selected, .selected:hover {
    background: lightblue;
    color: white;
  }

  .correct, .correct:hover {
    background: lightgreen;
    color: white;
  }

  .incorrect, .incorrect:hover {
    background: red;
    color: white;
  }
</style>