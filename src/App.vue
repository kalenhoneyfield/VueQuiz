<template>
  <div id="app">
    <Header v-bind:numCorrect="numCorrect" v-bind:numTotal="numTotal" />
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="0">
          <QuestionBox
            v-bind:currentQuestion="questions[index]"
            v-bind:next="next"
            v-bind:increment="increment"
            v-if="questions.length"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import './components/bootswatch/bootstrap-cerulean.css';
import Header from './components/Header';
import QuestionBox from './components/QuestionBox';

export default {
  name: 'App',
  components: {
    Header,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
    };
  },
  methods: {
    next: function() {
      const idx = this.index + 1;
      if (this.questions[idx]) {
        this.index++;
      } else {
        this.getQuestions();
        this.index = 0;
      }
    },
    increment: function(isCorrect) {
      if (isCorrect) {
        this.numCorrect++;
      }
      this.numTotal++;
    },
    getQuestions: function() {
      fetch('https://opentdb.com/api.php?amount=10&type=multiple', {
        method: 'GET',
      })
        .then(response => {
          return response.json();
        })
        .then(data => {
          this.questions = data.results;
        });
    },
  },
  mounted: function() {
    this.getQuestions();
  },
};
</script>

<style>
#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif; */
  /* font-family: 'Pacifico', cursive; */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 10px;
  background-color: var(--dark);
}
</style>
