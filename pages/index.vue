<template>
  <section class="container">
    <div>
      <h1 class="title">
        縦書きったー
      </h1>
      <h2 class="subtitle">
        縦書きツイートが簡単にできるサービス
      </h2>
      <p class="text-muted">
        ログインは必要ありません
      </p>
      <b-form-textarea
        v-model="inputText"
        class="mt-4"
        placeholder="ここに入力しすると"
        :rows="3"
      />
      <b-form-textarea
        :value="outputText"
        class="mt-4"
        placeholder="縦書きになって出てきます"
        :rows="9"
      />
      <b-button :variant="'primary'" class="m-4" @click="doTweet">
        <font-awesome-icon :icon="['fab', 'twitter']" />
        ツイートする
      </b-button>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      inputText: ''
    }
  },
  computed: {
    inputGyous() {
      const inputGyous = []
      const strings = this.inputText.split(/\r\n|\r|\n/)
      strings.forEach(string => {
        inputGyous.push(string)
      })
      return inputGyous
    },
    maxHeightOfinputGyous() {
      return this.inputGyous.length
    },
    maxWidthOfInputGyous() {
      return this.computeMaxWidhtOfArray(this.inputGyous)
    },
    outputGyous() {
      const outputGyous = []

      // 並び替える
      for (let w = 0; w < this.maxWidthOfInputGyous; w++) {
        let outputGyou = ''
        for (let h = this.maxHeightOfinputGyous - 1; h >= 0; h--) {
          let char
          if (w + 1 > this.inputGyous[h].length) {
            char = '　'
          } else {
            char = this.inputGyous[h].substr(w, 1)
          }
          outputGyou += char
        }
        outputGyous.push(outputGyou)
      }

      return outputGyous
    },
    outputText() {
      let outputText = ''
      this.outputGyous.forEach(gyou => {
        outputText += gyou + '\r'
      })
      return outputText
    },
    tweet() {
      return (
        'https://twitter.com/intent/tweet?url=' +
        encodeURIComponent(process.env.baseUrl) +
        '&text=' +
        encodeURIComponent(this.outputText) +
        '%0a%0a%23' +
        '縦書きったーで作成'
      )
    }
  },
  methods: {
    computeMaxWidhtOfArray(array) {
      let maxWidthOfArray = 0
      array.forEach(element => {
        if (element.length > maxWidthOfArray) {
          maxWidthOfArray = element.length
        }
      })
      return maxWidthOfArray
    },
    doTweet() {
      if (process.browser) {
        window.open(this.tweet)
      }
    }
  }
}
</script>

<style>
.container {
  margin-top: 2em;
}
</style>
