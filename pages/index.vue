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
      <b-form-group label="" class="mt-3 mb-3 text-left">
        <b-form-radio-group
          v-model="isblankRowInsert"
          buttons
          button-variant="outline-primary"
          size="sm"
          :options="options"
        />
      </b-form-group>
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
      <b-button :variant="'primary'" class="mt-3" @click="doTweet">
        <font-awesome-icon :icon="['fab', 'twitter']" />
        ツイートする
      </b-button>
    </div>
  </section>
</template>

<script>
import _ from 'underscore'
export default {
  data() {
    return {
      inputText: '',
      isblankRowInsert: false,
      options: [
        { text: '通常モード', value: false },
        { text: '一行飛ばし', value: true }
      ]
    }
  },
  computed: {
    inputRows() {
      const inputRows = this.inputText.split(/\r\n|\r|\n/)
      return this.isblankRowInsert ? this.insertBlankRow(inputRows) : inputRows
    },
    outputText() {
      return this.convertToVerticalWritingRows(this.inputRows).join('\r')
    },
    tweet() {
      return (
        'https://twitter.com/intent/tweet?url=' +
        encodeURIComponent(process.env.baseUrl) +
        '&text=' +
        encodeURIComponent(this.outputText) +
        '%0a%0a%23' +
        '縦書きったー%20で作成'
      )
    }
  },
  methods: {
    convertToVerticalWritingRows(inputRows) {
      return this.joinEachRowChars(
        this.replaceNobashibo(
          this.replaceUndefinedToSpace(_.zip(...inputRows.reverse()))
        )
      )
    },
    replaceUndefinedToSpace(rows) {
      return _.map(rows, row => {
        return _.map(row, cell => {
          return cell === undefined ? '　' : cell
        })
      })
    },
    replaceNobashibo(rows) {
      return _.map(rows, row => {
        return _.map(row, cell => {
          return cell === 'ー' ? '｜' : cell
        })
      })
    },
    joinEachRowChars(rows) {
      return _.map(rows, row => {
        return row.join('')
      })
    },
    insertBlankRow(rows) {
      const blankRowInsertedRows = []
      rows.forEach(row => {
        blankRowInsertedRows.push(row)
        blankRowInsertedRows.push('')
      })
      blankRowInsertedRows.pop()
      return blankRowInsertedRows
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
.title {
  font-size: 40px;
}
.subtitle {
  font-size: 20px;
}
</style>
