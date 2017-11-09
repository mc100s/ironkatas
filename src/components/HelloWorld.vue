<template>
  <div class="hello">
    <div v-if="errorMessage" v-html="errorMessage">
    </div>

    <kata-table v-else v-model="lastCodewarsPseudoUpdated"></kata-table>
    
  </div>
</template>

<script>
import KataTable from '@/components/KataTable'
import axios from 'axios'


export default {
  name: 'HelloWorld',
  components: {
    'kata-table': KataTable
  },
  data () {
    return {
      lastCodewarsPseudoUpdated: "",
      errorMessage: ""
    }
  },
  methods: {
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
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
