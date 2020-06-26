<template>
  <div >
    <b-jumbotron class="question-box-container">
      <template v-slot:header>{{'Category: '+currentQuestion.category}}</template>
      <template v-slot:lead>{{fixTypos(currentQuestion.question)}}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answerClass(index)"
        >{{fixTypos(answer)}}</b-list-group-item>
      </b-list-group>

      <b-button
        :cursor="pointer"
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button @click="next" variant="success" :disabled="!answered">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
    Activated: Boolean
  },

  data: function() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },

  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },

  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
        this.answered = false;
      }
    }
  },

  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index){
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index){
        answerClass = 'selected'
      }else if(this.answered && this.correctIndex === index){
        answerClass = 'correct'
      }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
        answerClass = 'incorrect'
      }

      return answerClass
      // [ !answered && selectedIndex === index ? 'selected': 
      //     answered && correctIndex === index ? 'correct' : 
      //     answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : '']
    },
    fixTypos(question){
      question = question.replace(/&quot;/g,"\"")
      question = question.replace(/&#039;/g,"'")
      question = question.replace(/&ldquo;/g,"“")
      question = question.replace(/&rdquo;/g,"”")

      return question
    }
  }
};
</script>

<style scoped>
.display-3{
  font-size: 1rem;
  background-color: lightgoldenrodyellow;
  border-radius: 40px;
}
.lead{
  background-color: white;
  border-radius: 10px;
}
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
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
.question-box-container{
  margin-top: 15px;
}
</style>