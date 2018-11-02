<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      Local Autocomplete
        <VueAutocomplete
            :start-value="''"
            :items="localItems"
            :return-amount="10"
            filter-key="label"
            :min-length="1" 
            name="search" 
            placeholder="Enter Programming language"
        >
        </VueAutocomplete>
    </p>
  </div>
</template>

<script>

import VueAutocomplete from './VueAutocomplete';

export default {
  
  name: 'HelloWorld',
  components: {
    VueAutocomplete
  },
  props: {
    msg: String
  },
  data: function() {
    return {
      localItems: [{id:1,label:'Javascript'},{id:2,label:'Java'},{id:3,label:'Python'},{id:4,label:'C#'}]
    }
  }
}
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
