<template>
	<header>
		<h2 style="margin: 0">CountryFinder</h2>
		<div class="filters">
			<select name="Continent" placeholder="Continent" v-model="this.selectedContinent">
				<option value="">All continents</option>
				<option v-for="continent in continents" :key="continent.name" :value="continent.name">
					{{ continent.name }}
				</option>
			</select>
			<select name="Languages" placeholder="Languages" v-model="this.selectedLanguage">
				<option value="">All languages</option>
				<option v-for="language in languages" :key="language.name" :value="language.name">
					{{ language.name }}
				</option>
			</select>
			<select name="Multi Languages" placeholder="Multilingual" v-model="this.selectedNumber">
				<option value="1">All types</option>
				<option value="2">Multilingual countries</option>
			</select>
		</div>
	</header>
	<main>
		<div class="main-container">
			<div class="country-card" v-for="country in filteredCountries" v-show="country.languages.length >= this.selectedNumber" :key="country">
				<p class="country-card-title">{{ country.name }}</p>
				<p>Capital: {{ country.capital }}</p>
				<p>Continent: {{ country.continent.name }}</p>
				<p>
					Languages:<br /><span v-for="language in country.languages" :key="language">{{ language.name }}<br /></span>
				</p>
				<p>Currency: {{ country.currency }}</p>
			</div>
		</div>
	</main>
	<footer></footer>
</template>

<script>
import axios from "axios";

const API_URL = "https://countries.trevorblades.com/graphql/";
const MAIN_QUERY = `
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
    },
    languages {
      name
    }
  }
`;

export default {
	data() {
		return {
			countries: [],
			continents: [],
			languages: [],
			selectedContinent: "",
			selectedLanguage: "",
			selectedNumber: 1,
		};
	},
	created() {
		this.fetchData();
	},
	methods: {
		async fetchData() {
			const url = API_URL;
			const allCountriesQuery = {
				query: MAIN_QUERY,
			};
			const result = await axios({
				url,
				method: "post",
				headers: {"content-type": "application/json"},
				data: allCountriesQuery,
			});
			const {countries, continents, languages} = result.data.data;
			this.countries = countries;
			this.continents = continents;
			this.languages = languages;
		},
	},
	computed: {
		filteredCountries() {
			if (!this.selectedContinent && !this.selectedLanguage) {
				return this.countries;
			}
			if (this.selectedLanguage && this.selectedContinent) {
				const filtered = this.countries.filter((element) => element.languages.some((language) => language.name === this.selectedLanguage) && element.continent.name === this.selectedContinent);
				console.log(filtered.length);
				return filtered;
			}
			if (this.selectedLanguage && !this.selectedContinent) {
				const filtered = this.countries.filter((element) => element.languages.some((language) => language.name === this.selectedLanguage));
				console.log(filtered.length);
				return filtered;
			}
			const filtered = this.countries.filter((element) => element.continent.name === this.selectedContinent);
			console.log(filtered.length);
			return filtered;
		},
	},
};
</script>
<style>
body {
	background-color: #fefefe;
	margin: 0;
}

p {
	font-family: Verdana, Geneva, Tahoma, sans-serif;
	margin: 0;
}

header {
	display: flex;
	justify-content: space-between;
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
