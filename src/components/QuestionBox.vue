<template>
  <div>
    <b-jumbotron>
      <template v-slot:lead>
        <b-badge right v-bind:variant="difficultyVariant(currentQuestion.difficulty)">
          {{ currentQuestion.difficulty }}
        </b-badge>
        <b-badge right variant="info">{{ currentQuestion.category }}</b-badge>
        <br />
        {{ currentQuestion.question | fixHTMLchars }}
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          v-bind:key="index"
          v-on:click="selectAnswer(index)"
          v-bind:variant="answerClass(index)"
          v-on:mouseover="handleHover(index)"
          v-on:mouseleave="handleHover(null)"
        >
          {{ answer | fixHTMLchars }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        v-on:click="submitAnswer"
        v-bind:disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button variant="success" v-on:click="next" v-bind:disabled="!answered">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';
import { AllHtmlEntities } from 'html-entities';
const entities = new AllHtmlEntities();

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
      isHovered: false,
    };
  },
  computed: {
    answers: function() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      return answers;
    },
  },
  methods: {
    selectAnswer: function(index) {
      return (this.selectedIndex = index);
    },
    shuffleAnswers: function() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
    },
    submitAnswer: function() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    answerClass: function(index) {
      // let answerClass = ''
      return !this.answered && this.selectedIndex === index
        ? 'info'
        : this.answered && this.correctIndex === index
        ? 'success'
        : this.answered && this.selectedIndex === index && this.correctIndex !== index
        ? 'danger'
        : '';
    },
    difficultyVariant: function(diff) {
      return diff === 'easy'
        ? 'success'
        : diff === 'medium'
        ? 'warning'
        : diff === 'hard'
        ? 'danger'
        : '';
    },
    handleHover: function(index) {
      this.isHovered = index;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler: function() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  filters: {
    fixHTMLchars: function(chars) {
      return entities.decode(chars);
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: var(--info);
  color: #ffffff;
  cursor: pointer;
}
.btn {
  margin: 0 5px;
}
.selected {
  background-color: teal;
}
.correct {
  background-color: yellowgreen;
}
.incorrect {
  background-color: tomato;
}
</style>
