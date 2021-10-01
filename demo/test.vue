<template>
  <div class="hello">
    <p>
      Ask a yes/no question:
      <input v-model="question">
    </p>
    <p>{{ answer }}</p>
  </div>
</template>

<script lang="ts">
  import Vue from 'vue'
  import _ from 'lodash'
  import axios from 'ts-axios-new'

  export default Vue.extend({
    name: 'HelloWorld',
    data () {
      return {
        question: '',
        answer: 'I cannot give you an answer until you ask a question!'
      }
    },
    created () {
      this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
    },
    methods: {
      debouncedGetAnswer () {
        // do nothing
      },
      getAnswer () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. -)'
          return
        }
        this.answer = 'Thinking...'
        const instance = axios.create()
        instance.interceptors.request.use((config) => {
          config.params = {
            _t: +new Date()
          }
          return config
        })

        instance.get('https://yesno.wtf/api')
          .then((response) => {
            this.answer = _.capitalize(response.data.answer)
          })
          .catch((error) => {
            this.answer = 'Error! Could not reach the API. ' + error
          })
      }
    },
    watch: {
      question: function (newQuestion: string, oldQuestion: string) {
        this.answer = 'Waiting for you to stop typing...'
        this.debouncedGetAnswer()
      }
    }
  })
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h3 {
    margin: 40px 0 0;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>
