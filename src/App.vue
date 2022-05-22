<template>
  <header>
    <h2 style="margin: 0;">CountryViewer</h2>
    <select name="Continent" aria-placeholder="Continent" v-model="this.selectedContinent" @change="filterByContinent">
      <option value="ALL">All continents</option>
      <option v-for="continent in continents" :key="continent.name" :value="continent.name">
        {{ continent.name }}
      </option>
    </select>
  </header>
  <main>
    <div class="main-container">
      <div class="country-card" v-for="country in filteredCountries" :key="country">
        <!-- <p>{{ country.emojiU.replace(/U\+/g, "&#x") }}</p> -->
        <p class="country-card-title">{{country.name}}</p>
        <p>Capital: {{country.capital}}</p>
        <p>Continent: {{country.continent.name}}</p>
        <p>Languages:<br><span v-for="language in country.languages" :key="language">{{ language.name }}<br></span></p>
        <p>Currency: {{country.currency}}</p>
      </div>
    </div>
  </main>
  <footer></footer>
</template>

<script>
import axios from 'axios';

const API_URL = 'https://countries.trevorblades.com/graphql/';
const MAIN_QUERY =`
  query {
        countries {
          name,
          capital,
          continent {name},
          languages {name},
          currency,
          emojiU
        },
        continents {
          name,
          countries {name}
    }
  }
`

export default {
  data() {
    return {
      countries: [],
      continents: [],
      selectedContinent: "ALL",
      filteredCountries: [],
    }
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const url = API_URL;
      const allCountriesQuery = {
        "query": MAIN_QUERY
      };
      const result = await axios({
        url,
        method: 'post',
        headers: {"content-type": "application/json"},
        data: allCountriesQuery,
      });
      const {countries, continents} = result.data.data;
      this.countries = countries;
      this.continents = continents;
    },
    filterByContinent() {
      
    }
  },
  computed: {
    filteredCountries() {
      if (this.selectedContinent === "ALL") {
        return this.countries;
      }
      const filtered = this.countries.filter((element) => element.continent.name === this.selectedContinent);
      return filtered;
    }
  }
}
</script>
<style>
body {
  background-color: #fefefe;
  margin: 0;
}

p{
  font-family:Verdana, Geneva, Tahoma, sans-serif;
  margin: 0;
}

header {
  width: 100%;
  min-height: 10px;
  padding: 0.5em;
  background-color: #fff;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 1px 1px;
  position: fixed;
}

main {
  padding: 1em;
}

.main-container {
  margin-top: 2.5em;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  gap: 1ch;
}

.country-card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-radius: 15px;
  background-color: #fff;
  padding: 1em;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 12px;
}

.country-card p {
  margin-bottom: 1rem;
}

.country-card-title {
  font-size: 1.4rem;
}
</style>
