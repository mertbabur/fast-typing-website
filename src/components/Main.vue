<template>
  <div class="container jumbotron">
    <h1 class="display-4">HIZLI YAZMA YARIŞMASI</h1>
    <p class="lead">NE KADAR HIZLI KLAVYE KULLANDIĞINI TEST ET</p>
    <div>
      Dogru sayısı : {{trueCount}}
      Yanlis sayısı : {{falseCount}}
    </div>
    <hr class="my-4" />

    <div v-if="isFinish" class="alert alert-primary">
      <h3>SÜRE BİTTİ</h3>
      <p class="display-4">{{dks}} DKS</p>
      <span>Doğruluk Yüzdesi : {{truePercent}}</span><br>
      <span>Doğru Kelime Sayısı : {{trueCount}}</span><br>
      <span>Yanlış Kelime Sayısı : {{falseCount}}</span><br><br>

      <button @click="newGame" class="container btn btn-success btn-block">Yeni Oyun</button>

    </div>
    <div v-else>
    <div class="card">
      <div class="card-body">
          <span v-for="(word,key) in words.filter((data, index) => index < 20)" :key="key" v-bind:class="key==0 ? writingWordControl : null" class="m-2 words">
            {{word}}
          </span>
      </div>
    </div>
    <div class="card">
      <div class="card-body">
        <div class="input-group input-group-lg">
          <input
            type="text"
            class="form-control"
            v-model="writingWord"
            placeholder="Recipient's username"
            aria-label="Recipient's username with two button addons"
            aria-describedby="button-addon4"
          />
          <div class="input-group-append" id="button-addon4">
            <button class="btn btn-outline-secondary" disabled type="button">
              {{timer}} sn.
            </button>
            <button :disabled="isRunning" class="btn btn-outline-secondary" type="button" @click="getWords">
              Yenile
            </button>
          </div>
        </div>
      </div>
    </div>
    </div>
  </div>
</template>

<script>

import wordList from '@/assets/words.json'

export default {
  data () {
    return {
      words: [],
      writingWord: null,
      isTrue: true,
      trueCount: 0,
      falseCount: 0,
      timer: 5,
      interval: false,
      isRunning: false,
      isFinish: false,
      wordList: wordList
    }
  },
  mounted () {
    this.getWords()
  },
  watch: {
    writingWord (val) {
      if (!this.isRunning) {
        this.toggleTimer()
      }

      const word = this.words[0].slice(0, val.length)
      const userWord = val.replace(' ', '')
      this.isTrue = word === userWord

      if (val.indexOf(' ') !== -1) {
        this.isTrue ? this.trueCount++ : this.falseCount++
        this.words.splice(0, 1)
        this.writingWord = ''
        this.isTrue = true
      }
    }
  },
  computed: {
    writingWordControl () {
      return this.isTrue ? 'writing-word' : 'writing-word bg-danger'
    },
    dks () {
      return 300 - this.words.length
    },
    truePercent () {
      const percent = (100 / this.dks)
      return (percent * this.trueCount)
    }
  },
  methods: {
    newGame () {
      this.getWords()
      this.isFinish = false
      this.timer = 60
      this.isTrue = true
      this.isRunning = false
    },
    getWords () {
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300)
    },
    toggleTimer () {
      this.isRunning = true
      this.interval = setInterval(this.timeProcess, 1000)
    },
    timeProcess () {
      if (this.timer === 0) {
        clearInterval(this.interval)
        this.isFinish = true
        return
      }
      this.timer--
    }
  }
}

</script>

<style>
  .words{
    font-size: 25px;
    font-weight: 400px;
  }

  .writing-word{
    background-color: gray;
    color: white;
    padding: 5px;
    border-radius: 5px;
  }
</style>
