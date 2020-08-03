<template>
  <div class="home">
    <p id="timer">Not started</p>
    <p id="text"></p>
    <input id="input" type="text">
    <p id="facts"></p>
    <br><br><hr>
    <!--<a href="https://data.typeracer.com/pit/profile?user=nikolaj808&ref=badge" target="_top"><img src="https://data.typeracer.com/misc/badge?user=nikolaj808" border="0" alt="TypeRacer.com scorecard for user nikolaj808"/></a>-->
  </div>
</template>

<script>
import store from '@/store';

export default {
  name: 'Home',
  store,
  data () {
    return {
      quote: {},
      sentence: '',
      split: '',
      index: 0,
      wrongs: 0,
      canEdit: true,
      words: 0,
      firstInput: true,
      start: 0,
      end: 0
    }
  },
  methods: {
    handleInput (e) {
      if (this.firstInput) {
        this.start = Date.now()
        this.firstInput = false
        document.getElementById('timer').innerText = 'Started'
      }
      if (this.canEdit) {
        if (this.index === this.sentence.length - 1 && this.sentence[this.index] === String.fromCharCode(e.keyCode)) {
          this.end = Date.now()
          const timeInSeconds = (this.end - this.start) / 1000
          const wpm = (this.words / timeInSeconds) * 60
          this.canEdit = false
          document.getElementById('text').innerText = 'That took ' + timeInSeconds + ' seconds. There was ' + this.words + ' words. Your speed was ' + Math.ceil(wpm) + 'wpm'
          document.getElementById('facts').innerText = 'That was a quote from ' + this.quote.from + '. It was said by ' + this.quote.saidby + '. It was from ' + this.quote.episode + '.'
        } else {
          if (this.sentence[this.index] === String.fromCharCode(e.keyCode) && this.wrongs === 0) {
            this.split[this.index] = this.sentence[this.index].fontcolor('green')
            this.index++
            document.getElementById('text').innerHTML = this.split.join('')
          } else {
            this.split[this.index] = this.sentence[this.index].fontcolor('red')
            document.getElementById('text').innerHTML = this.split.join('')
            this.wrongs++
          }
        }
      }
    },
    handleDelete (e) {
      if (this.canEdit) {
        if (e.keyCode === 8) {
          if (this.index > 0 && this.wrongs === 0) {
            this.index--
            this.split[this.index] = this.sentence[this.index]
            document.getElementById('text').innerHTML = this.split.join('')
          } else if (this.wrongs > 0) {
            this.wrongs--
            if (this.wrongs === 0) {
              this.split[this.index] = this.sentence[this.index].fontcolor('black')
              document.getElementById('text').innerHTML = this.split.join('')
            }
          }
        }
      }
    }
  },
  mounted () {
    this.quote = this.$store.state.quotes[Math.floor(Math.random() * this.$store.state.quotes.length)]
    this.sentence = this.quote.text
    console.log(this.sentence)
    this.split = this.sentence.split('')
    this.words = this.sentence.split(' ').length
    document.getElementById('input').addEventListener('keypress', this.handleInput)
    document.getElementById('input').addEventListener('keydown', this.handleDelete)
    document.getElementById('text').innerHTML = this.sentence
  }
}
</script>

<style scoped>
  input {
    min-width: 95vw;
  }

  p {
    font-size: 24px;
    font-weight: bolder;
    color: black;
  }
</style>
