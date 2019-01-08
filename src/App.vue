<template lang="pug">
  .search
    h1.search-title Star Wars
    form.search-form(@keydown.enter.prevent="searchPeople()")
      input.search-bar(type="text" v-model="search" list="autocomplete" @input="autocomplete()")
      datalist#autocomplete
        option(v-for="offer in autocompleteResponse.data") {{ offer.name }}
      button.search-button(type="button" @click="searchPeople()") Search
    .results(v-if="response")
      .result(v-for="result in response.data")
        | {{ result.name }}
        br
        | {{ result.species }}
        br
        | {{ result.homeworld }}
</template>

<script>
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      search: '',
      response: {},
      autocompleteResponse: {}
    }
  },
  methods: {
    async searchPeople () {
      if (this.search) {
        this.response = await axios.get(`http://localhost:3000/api/match/${this.search}`)
        console.log(this.response.data)
      }
    },
    async autocomplete () {
      if (this.search) {
        this.autocompleteResponse = await axios.get(`http://localhost:3000/api/autocomplete/${this.search}`)
        console.log(this.autocompleteResponse.data)
      }
    }
  }
}
</script>

<style lang="sass">
.search
  display: flex
  flex-direction: column
  align-items: center

.results
  padding: 20px
  .result
    padding: 5px
    border: 1px solid #f0f0f0
</style>
