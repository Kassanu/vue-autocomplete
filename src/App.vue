<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Vue-Autocomplete Demo"/>
  </div>
</template>

<script>
import axios from 'axios';
import _ from 'lodash'; 
import HelloWorld from './components/HelloWorld.vue'
import VueAutocomplete from './components/VueAutocomplete.vue'
import VueAutocompleteItem from './components/VueAutocompleteItem.vue'

export default {
  name: 'app',
  components: {
    HelloWorld,
    VueAutocomplete,
    VueAutocompleteItem
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
  margin-top: 60px;
}
</style>
