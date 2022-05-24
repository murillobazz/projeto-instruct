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
		<button style="margin-right: 20px;" @click="downloadQueryFile()">Download query (.csv)</button>
	</header>
	<main>
		<div class="main-container">
			<div class="country-card" v-for="country in filteredCountries" v-show="country.languages.length >= this.selectedNumber" :key="country">
				<p class="country-card-title">{{ country.name }}</p>
				<p><small>Capital:</small> {{ country.capital }}</p>
				<p><small>Continent:</small> {{ country.continent.name }}</p>
				<p>
					<small>Languages:</small><br /><span v-for="language in country.languages" :key="language">{{ language.name }}<br /></span>
				</p>
				<p><small>Currency:</small> {{ country.currency }}</p>
			</div>
		</div>
	</main>
	<footer>
		<p>
			<small>Criado por <a href="https://github.com/murillobazz" target=_blank>Murillo Bazilio</a></small>
		</p>
	</footer>
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
		downloadQueryFile() {
			const csvString = [["Name", "Capital", "Continent", "Languages", "Currency"],
			...this.filteredCountries.map((item) => [
				item.name,
				item.capital,
				item.continent.name,
				item.languages.map((language) => [language.name]).join("; "),
				String(item.currency).replaceAll(",", "; ")
				// Não utilizar o método String() estava retornando um bug quando não utilizávamos nenhum filtro antes de exportar.
			])];
			console.log(csvString);
			const csvContent = "data:text/csv;charset=utf-8," + csvString.map(item => item.join(",")).join("\n");
			const csvFile = encodeURI(csvContent);
			const anchor = document.createElement('a');
			anchor.href = csvFile;
			anchor.target = "_blank";
			anchor.download = `CountriesQuery_${new Date().toLocaleString()}.csv`;
			anchor.click();
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
<style scoped>
body {
	background-color: #fefefe;
	margin: 0;
}

* {
	font-family: "Roboto", Verdana, Geneva, Tahoma, sans-serif;
	margin: 0;
}

header {
	display: flex;
	justify-content: space-between;
	width: 100%;
	padding: 0.5em 1em;
	background-color: #fff;
	box-shadow: rgba(0, 0, 0, 0.1) 0px 1px 1px;
	position: fixed;
	top: 0;
	left: 0;
}

.filters {
	display: flex;
	justify-content: center;
	gap: 1ch;
}

main {
	padding: 1em;
}

.main-container {
	max-width: 100%;
	margin-top: 2.5em;
	display: grid;
	grid-template-columns: 1fr 1fr 1fr 1fr;
	gap: 1ch;
}

.country-card {
	display: flex;
	flex-direction: column;
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

footer {
	width: 100%;
	margin: 5px 0;
	display: flex;
	justify-content: center;
}

@media (max-width: 767px) {
	header {
		flex-direction: column;
		gap: 0.5ch;
	}

	header button {
		align-self: center;
	}

	.filters {
		flex-direction: column;
		align-items: center;
	}

	.main-container {
		margin-top: 8em;
		grid-template-columns: 1fr 1fr;
		gap: 0.5ch;
	}
}




</style>
