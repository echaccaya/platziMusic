<template lang="pug">
  #app
    img(src='./assets/logo.png')
    h1 {{ msg }}
    select(v-model="selectedCountry" v-show="loadingCountries")
      option(v-for="country in countries" :value="country.name") {{country.name}}
    spinner(v-show="loading")
    ul
      artist(v-for="artist in artists" :artist="artist" :key="artist.mbid")
</template>

<script>
import Artist from './components/Artist.vue';
import Spinner from './components/Spinner.vue';
import {getCountries, getArtists} from "./api";

export default {
  name: 'app',
  data () {
    return {
      msg: 'Platzi Music',
      artists: [],
      countries: [],
      selectedCountry: "Spain",
      loading: true,
      loadingCountries: false
    }
  },
  components: {
    Artist,
    Spinner
  },
  methods: {
    refreshArtists() {
      const self = this;
      
      self.artists = [];
      self.loading = true;
      
      getArtists(this.selectedCountry)
        .then(function(artists) {
          self.loading = false;

          self.artists=artists;
        })
        .catch(error => console.error(error))
        .finally(() => self.loading = false);
    },
    loadCountries() {
      const self = this;
      getCountries()
        .then(function(countries) {
          self.countries=countries;

          self.loadingCountries=true;
        });
    }
  },
  mounted() {
    this.loadCountries();
    this.refreshArtists();
  },
  watch: {
    selectedCountry() {
      this.refreshArtists()
    }
  }
}
</script>

<style lang="stylus">
  #app
    font-family 'Avenir', Helvetica, Arial, sans-serif
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    text-align center
    color #2c3e50
    margin-top 60px

  h1, h2
    font-weight normal

  ul
    list-style-type none
    padding 0

  li
    display inline-block
    margin 0 10px

  a
    color #42b983
</style>
