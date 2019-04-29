<template>
<div>
  <label>Enter stock symbol(s) separated by a comma</label>
<input type="text" v-model="symbol" placeholder="e.g. aapl,goog"></input>
<button @click="getQuote">Get Quote</button>
<button @click="updateQuotes">Update Quotes</button>
<button @click="clearHistory">Clear History</button>

<template v-if="updateTimes.length">
<h2>Update Time History</h2>
  </template>
<ul>

// add unique key
<li v-for="time in updateTimes" :key="time"> {{time}}</li>
</ul>

<p v-if="this.quotes[0] && this.quotes[0].length">Most Recent Update Time: {{updateTime}}</p>
<template v-for="quote in quotes[0]">
  // add unique keys
  <h3 :key="quote.symbol">Result for {{quote.symbol}}</h3>
  <p :key="quote.lastTradePrice">Last trade price: {{quote.lastTradePrice}}</p>
  </template>
</div>
</template>

<script>
export default {
  name: 'StockQuote',
  data() {
    return {
      symbol: "",
      quotes: [],
      stocksDisplayed: "",
      updateTime: null,
      updateTimes: []
    }
  },
  methods: {
    getQuote() {
      this.quotes = []
      fetch(`http://candidateservices.allegient.com/randomQuote/quote?symbols=${this.symbol}`)
        .then(data => data.json())
        .then(data => {
          this.quotes.push(data.quotes)
          this.updateTime = data["generatedDate"]
          this.updateTimes.push(data["generatedDate"])
        })
      this.stocksDisplayed = this.symbol
      this.symbol = ""
    },
    updateQuotes() {
      fetch(`http://candidateservices.allegient.com/randomQuote/quote?symbols=${this.stocksDisplayed}`)
        .then(data => data.json())
        .then(data => {
          this.quotes = []
          this.updateTime = data["generatedDate"]
          this.updateTimes.push(data["generatedDate"])
          this.quotes.push(data.quotes)
        })
    },
    clearHistory() {
      this.quotes = []
      this.updateTime = null
      this.stocksDisplayed = ""
      this.updateTimes = []
    }

  }
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
label {
  display: block
}
li {
  margin: 0 10px;
}
a {
  color: #42b983;
}
p {
  margin-top: 30px
}
</style>
