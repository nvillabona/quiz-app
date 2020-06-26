<template>
  <div id="app">
    <Header 
    :numCorrect="numCorrect"
    :numTotal ="numTotal"/>

    <b-container >
      <b-row >
        <b-col sm="6" offset-sm="3" cols="12">
          <QuestionBox 
          v-if="questions.length > 0 "
          :currentQuestion="questions[index]"
          :next="next"
          :increment="increment" />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QuestionBox from "./components/QuestionBox.vue";

export default {
  name: "App",
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
      Activated: true
    };
  },
  methods: {
    next() {
      this.index++;
    },
    increment(isCorrect){
      if(isCorrect){
        this.numCorrect++
      }
      this.numTotal++
    }
  },
  mounted: function() {
    fetch("https://opentdb.com/api.php?amount=10", {
      method: "get"
    })
      .then(response => {
        return response.json();
      })

      .then(jsonData => {
        this.questions = jsonData.results;
        
      });
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
}
</style>
