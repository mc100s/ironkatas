<template>
  <div class="flex-center">
    <span class="is-hidden"> {{ value }}</span>
    <table class="table">
      <thead>
        <tr>
          <th>Katas</th>
          <th>Info</th>
          <th v-for="student in students"><abbr :title="student.firstname">{{ student.codewarsPseudo }}</abbr></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="kata in katas.filter(kata => !kata.other)">
          <td><a :href="'https://www.codewars.com/kata/' + kata.slug">{{ kata.slug }}</a></td>
          <td class="has-text-centered"><button v-if="getKataRank(kata)" :class="'button is-small'">{{ getKataRank(kata) }}</button></td>
          <td v-for="student in students" class="has-text-centered">
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
          <th></th>
          <th v-for="student in students"></th>
        </tr>
        <tr v-for="kata in katas.filter(kata => kata.other)">
          <td><a :href="'https://www.codewars.com/kata/' + kata.slug">{{ kata.slug }}</a></td>
          <td><button v-if="getKataRank(kata)" :class="'button is-small'">{{ getKataRank(kata) }}</button></td>
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
          firstname: "Antoine",
          codewarsPseudo: "amarc27",
        },
        {
          firstname: "Charlotte",
          codewarsPseudo: "charlottebtmn",
        },
        {
          firstname: "David",
          codewarsPseudo: "dmc1985",
        },
        {
          firstname: "Julien",
          codewarsPseudo: "jb1404"
        },
        {
          firstname: "Maxence",
          codewarsPseudo: "mc100s",
        },
        {
          firstname: "Medhi",
          codewarsPseudo: "Medhi"
        },
        {
          firstname: "Vivian",
          codewarsPseudo: "VivianSolide",
        },
      ],
      katas: [
        {slug: "multiply", rank: "8 (bugs)"},
        {slug: "even-or-odd", rank: "8 (maths)"},
        {slug: "youre-a-square", rank: "7 (maths)"},
        {slug: "jaden-casing-strings", rank: "7 (strings)"},
        {slug: "sort-array-by-string-length", rank: "7 (sort)"},
        {slug: "pete-the-baker", rank: "7 (objects)"},
        {slug: "powers-of-3", rank: "7 (maths)"},
        {slug: "folding-your-way-to-the-moon", rank: "7 (maths)"},
        {slug: "reversed-strings", rank: "7 (strings)"},
        {slug: "multiples-of-3-or-5", rank: "6 (maths)"},
        {slug: "write-number-in-expanded-form", rank: "6 (maths)"},
        {slug: "custom-array-dot-concat-method", rank: "6 (arrays)"},
        {slug: "scramblies", rank: "5 (strings)"},
        {slug: "moving-zeros-to-the-end", rank: "5 (arrays)"},
        {slug: "valid-parentheses", rank: "5 (strings)"},
        {slug: "hello-w-dot-dot-dot-wait-what", rank: "4 (puzzle)"},
        {slug: "largest-product-in-a-series-1", rank: "(maths)"},
      ],
      errorMessage: ""
    }
  },
  methods: {
    getKataRank(kata) {
      if (!kata.rank) {
        // axios.get(`https://www.codewars.com/api/v1/code-challenges/${kata.slug}`)
        //   .then((response) => {
        //     kata.rank = response.data.rank.name
        //     kata.color = response.data.rank.color
        //     this.$emit('input', Math.random());
        //   })

      }
      return kata.rank;
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

.flex-center{
  display: flex;
  justify-content: center;
}
</style>
