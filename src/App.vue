<template>
  <div>
    <ScoreBoard />
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>      
      <template v-for="(answer, index) in this.answers" :key="index">
        <input type="radio" name="options" 
          :disabled="this.answerSubmitted"
          :value="answer"
          v-model="this.chosenAnswer">
        <label v-html="answer"></label><br>
      </template>

      <button class="send" type="button"
        v-if="!this.answerSubmitted"
        @click="this.submitAnswer()">Send</button>

      <section class="result" v-if="this.answerSubmitted">
        <h4 v-if="this.chosenAnswer == this.correctAnswer">&#9989; Congratulations, you got it right!</h4>
        <h4 v-else
          v-html="'&#10060; You picked the wrong answer.  The correct answer is ' + this.correctAnswer"></h4>
        <button class="send" type="button"
          @click="this.getNewQuestion()">Next question</button>
      </section>
    </template>
 </div>
</template>

<script>
import ScoreBoard from '@/components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false
    }
  },
  computed: {
    answers() {
      var answers = [...this.incorrectAnswers];
      answers.splice(Math.round(Math.random() * 4), 0, this.correctAnswer);
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('Please select an answer');
      } else {
        this.answerSubmitted = true;        
        if (this.chosenAnswer == this.correctAnswer) {
          console.log("You got it right!")
        } else {
          console.log("That is not correct");
        }
      }
    },

    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
          console.log(response.data.results[0])   
        })
    }
  },
  
  created() {
    this.getNewQuestion();
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  max-width: 960px;

  .option {
    display: flex;
    align-items: center;
    margin-bottom: 8px;
  }

  input[type="radio"] {
    margin: 12px 4px; 
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #ffff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
