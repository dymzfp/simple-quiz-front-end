<template>
  <div id="app">
    <Header 
      :correctTotal="correctTotal"
      :answeredTotal="answeredTotal"
    />

    <br>
    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset-sm="3">
          <QuestionBox 
            v-if="!isLoading"
            :currentQuestion="questions[index]"
            :next="next"
            :increment="increment"
          />

          <loading 
            :active.sync="isLoading" 
            :can-cancel="false" 
            :is-full-page="true">
          </loading>
        </b-col>
      </b-row>
    </b-container>

    <div>
      <b-modal ref="my-modal" hide-footer title="Result">
        <div class="d-block text-center">
          <h3>Scors : {{ this.result }}</h3>
        </div>
        <b-button class="mt-3" block @click="refresh">Play Again</b-button>
      </b-modal>
    </div>

  </div>
</template>

<script>
import Header from './components/Header'
import QuestionBox from './components/QuestionBox'

import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox,
    Loading
  },
  data() {
    return {
      questions: [],
      index: 0,
      isLoading: true,
      correctTotal: 0,
      answeredTotal: 0,
      questionsTotal: 0,
      result: 0,
      finish: false
    }
  },
  methods: {
    fetchData() {
      fetch("https://opentdb.com/api.php?amount=10&category=27&type=multiple", {
        method: 'get'
      })
        .then(response => {
          return response.json()
        })
          .then(data => {
            this.questions = data.results
            this.isLoading = false
            this.questionsTotal = this.questions.length
          })
    },
    next() {
      if (this.index < this.questions.length - 1) {
        this.index++
      }
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.correctTotal++
      }

      this.answeredTotal++

      if (this.answeredTotal == this.questionsTotal) {
      // if (this.answeredTotal == 2) {
        this.result = this.correctTotal * 10
        this.finish = true

        // setTimeout(() => {  this.showModal() }, 2000);
        this.showModal()
      }
    },
    showModal() {
      this.$refs['my-modal'].show()
    },
    hideModal() {
      this.$refs['my-modal'].hide()
    },
    refresh() {
      this.hideModal()

      this.index = 0,
      this.isLoading = true,
      this.correctTotal = 0,
      this.answeredTotal = 0,
      this.result = 0,
      this.finish = false

      this.fetchData()

    }
  },
  mounted: function() {
    this.fetchData()
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
}
</style>
