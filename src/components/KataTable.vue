<template>
  <div>
    <span class="is-hidden"> {{ value }}</span>
    <table class="table">
      <thead>
        <tr>
          <th>Katas</th>
          <th v-for="student in students"><abbr :title="student.firstname">{{ student.codewarsPseudo }}</abbr></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="kata in katas.filter(kata => !kata.other)">
          <td><a :href="'https://www.codewars.com/kata/' + kata.slug">{{ kata.slug }}</a></td>
          <td v-for="student in students">
            <button class="button is-primary is-small" v-if="student.katasSolved && student.katasSolved.map(kata => kata.slug).indexOf(kata.slug) !== -1">
              <i class="fa fa-check"></i>
            </button>
            <!--
            <pre v-if="student">{{ student }}</pre>
            <pre v-if="student.katasSolved">{{ student.katasSolved.filter(kata => kata.slug === kataSlug).map() }}</pre>
            <span v-if="student.katasSolved && student.katasSolved.map(kata => kata.slug).indexOf(kataSlug)">Done</span>
            -->
          </td>
        </tr>
        <tr class="is">
          <th>Other Katas</th>
          <th v-for="student in students"></th>
        </tr>
        <tr v-for="kata in katas.filter(kata => kata.other)">
          <td><a :href="'https://www.codewars.com/kata/' + kata.slug">{{ kata.slug }}</a></td>
          <td v-for="student in students">
            <button class="button is-primary is-small" v-if="student.katasSolved && student.katasSolved.map(kata => kata.slug).indexOf(kata.slug) !== -1">
              <i class="fa fa-check"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'KataTable',
  props: ["value"],
  data () {
    return {
      students: [
        {
          firstname: "Maxence",
          codewarsPseudo: "mc100s",
        },
        // {
        //   firstname: "Charlotte",
        //   codewarsPseudo: "Charlotte",
        // },
        {
          firstname: "Antoine",
          codewarsPseudo: "amarc27",
        },
        {
          firstname: "Vivian",
          codewarsPseudo: "VivianSolide",
        },
        {
          firstname: "David",
          codewarsPseudo: "dmc1985",
        },
      ],
      katas: [
        {slug: "multiply"},
        {slug: "hello-w-dot-dot-dot-wait-what"},
        {slug: "largest-product-in-a-series-1"},
        {slug: "largest-product-in-a-series-2"}
      ],
      errorMessage: ""
    }
  },
  watch: {
    'students': {
      handler: function(val) { console.log('changed!!!') },
      deep: true
    }
  },
  created() {
    for (let i in this.students) {
      let codewarsPseudo = this.students[i].codewarsPseudo;
      console.log(codewarsPseudo);
      axios.get(`https://www.codewars.com/api/v1/users/${codewarsPseudo}/code-challenges/completed?page=0&access_key=Z1kdTZsiVQxYSaXzEnZV`)
        .then((response) => {
          let newStudent = this.students[i];
          newStudent.katasSolved = response.data.data
          this.$emit('change', this.students);
          this.$emit('input', codewarsPseudo);

          // Update $this.katas to push other katas solved by the students
          for (var j = 0; j < newStudent.katasSolved.length; j++) {
            let slug = newStudent.katasSolved[j].slug;
            if (slug && this.katas.map(kata => kata.slug).indexOf(slug) === -1) 
              this.katas.push({
                slug,
                other: true
              })
          }
        })
        .catch(error => {
          console.log("Axios error: ", error)
          this.errorMessage = `
          <p>You should install the CORS Chrome plugin
            <a href='https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi?hl=en'>here</a>.
          </p>
          <p>Then, we advise you to only add 'https://www.codewars.com/api/v1/' as intercepted URL.</p>
          <p><img src='/static/img/screenshot-cors.png'></p>`
        })
    }
  }
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
