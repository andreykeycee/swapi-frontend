<template lang="pug">
  .search
    h1.search-title Star Wars
    form.search-form(@keydown.enter.prevent="searchPeople()")
      input.search-bar(type="text" v-model="search" @input="autocomplete()" @focus="showAutocomplete = true")
      ul.autocomplete(v-show="showAutocomplete")
        li(v-for="offer in autocompleteResponse.data" :key="offer.name" @click="search = offer.name, showAutocomplete = false") {{ offer.name }}
      button.search-button(type="button" @click="searchPeople()") Search
    .results(v-if="response")
      .result(v-for="result in response.data")
        | {{ result.name }}
        br
        | {{ result.species }}
        br
        | {{ result.homeworld }}
        br
        | Your match: {{ Math.floor(result.total) }}
    .error-results(v-if="error") Sorry, your request have no results, choose one of these
        .result(v-for="result in errorResponse.data" @click="search = result.name, searchPeople()") {{ result.name }}
</template>

<script>
import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      search: '',
      response: {},
      errorResponse: {},
      error: false,
      autocompleteResponse: {},
      showAutocomplete: false
    }
  },
  methods: {
    async searchPeople () {
      if (this.search) {
        const { data } = await axios.get(`http://localhost:3000/api/match/${this.search}`)
        if (data.errors) {
          this.response = {}
          this.error = true
          this.errorResponse = await axios.get(`http://localhost:3000/api/autocomplete/${this.search}`)
        } else {
          this.error = false
          this.response = { data }
        }
      }
      this.showAutocomplete = false
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

.search-form
  position: relative

.search-bar
  height: 15px

.autocomplete
  position: absolute
  top: 0
  left: -35px
  list-style: none
  margin-top: 22px
  background: white
  li:hover
    cursor: pointer
    background: lightgray

.results
  padding: 20px

.result
  padding: 5px
  border: 1px solid #f0f0f0

.error-results
  .result
    cursor: pointer
    &:hover
      background: lightgray

</style>
