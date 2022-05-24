<template>
	<div>
		<header>
			<h1 class="title">Country<span class="title" style="color: #00B772">Finder</span></h1>
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
			<button @click="downloadQueryFile()">Download query (.csv)</button>
		</header>
		<section>
			<div class="main-container">
				<div class="country-card" v-for="country in filteredCountries" v-show="country.languages.length >= this.selectedNumber" :key="country">
					<p class="country-card-title">{{ country.name }}</p>
					<span class="country-card-subtitle">Capital:</span><p><b>{{ country.capital }}</b></p>
					<span class="country-card-subtitle">Continent:</span><p><span :class="this.formattedContinent(country.continent.name)"><b>{{ country.continent.name }}</b></span></p>
					<span class="country-card-subtitle">Languages:</span>
					<div class="country-card-languages">
						<span class="language-tag" v-for="language in country.languages" :key="language">{{ language.name }}</span>
					</div>
					<span class="country-card-subtitle">Currency:</span><p><i>{{ country.currency }}</i></p>
				</div>
			</div>
		</section>
		<footer>
			<p>
				<small>Created by <a href="https://github.com/murillobazz" target=_blank>Murillo Bazilio</a></small>
			</p>
		</footer>
	</div>
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
		formattedContinent(continentName) {
			if (continentName === "Asia") return "orange";
			if (continentName === "Africa") return "yellow";
			if (continentName === "Antarctica") return "blue";
			if (continentName === "Europe") return "burgundy";
			if (continentName === "North America") return "light-green";
			if (continentName === "South America") return "dark-green";
			if (continentName === "Oceania") return "purple";
			else return "#000000";
		}
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

* {
	font-family: "Roboto", Verdana, Geneva, Tahoma, sans-serif;
	margin: 0;
}

header {
	display: flex;
	justify-content: space-between;
	width: 100%;
	max-width: 100%;
	padding: 10px;
	background-color: #f7f7f7;
	box-shadow: rgba(0, 0, 0, 0.1) 0px 1px 1px;
	position: fixed;
	top: 0;
	left: 0;
}

.title {
	font-family: "Quicksand","Roboto", Verdana, Geneva, Tahoma, sans-serif;
	font-weight: bold;
}

select {
	border-radius: 15px;
	padding: 0 5px;
}

header button {
	border-radius: 10px;
	margin-right: 25px;
	padding: 0 10px;
	border: solid 1px #ddd;
	color: #fff;
	background-color: #00B772;
	font-weight: bold;
}

.filters {
	display: flex;
	justify-content: center;
	gap: 1ch;
}

section {
	background-color: #fff;
	max-width: 100%;
}

.main-container {
	padding: 1em;
	width: fit-content;
	margin-top: 3.5em;
	display: grid;
	grid-template-columns: repeat(4, minmax(0, 1fr));
	gap: 2ch;
}

.country-card {
	display: flex;
	flex-direction: column;
	border-radius: 15px;
	background-color: #f7f7f7;
	padding: 1em;
	box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
}

.country-card p {
	margin-bottom: 1rem;
}

.country-card-title {
	font-size: 1.4rem;
	font-weight: bold;
	padding-bottom: 10px;
	border-bottom: solid 1px rgba(0, 183, 114, 0.3);
}

.country-card-subtitle {
	font-size: 12px;
	margin-bottom: 5px;
}

.country-card-languages {
	display: grid;
	grid-template-columns: 1fr 1fr;
	gap: 0.5ch;
	margin-bottom: 10px;
	max-width: 50px;
}

.language-tag {
	display: flex;
	align-items: center;
	justify-content: center;
	text-align: center;
	font-size: 12px;
	border: solid 1px #00B772;
	border-radius: 20px;
	padding: 5px 10px;
	word-wrap: break-word;
}

footer {
	width: 100%;
	margin: 5px 0;
	display: flex;
	justify-content: center;
}

.orange {
	color: #ff8800;
}
.yellow {
	color: #FBD428;
}
.blue {
	color: #000080;
}
.burgundy {
	color: #c70032;
}
.light-green {
	color: #32CD32;
}
.dark-green {
	color: #006400;
}
.purple {
	color: #4a04a0;
}

@media (max-width: 767px) {
	header {
		flex-direction: column;
		padding: 5px 0;
		gap: 1ch;
	}

	.title {
		align-self: center;
	}

	header button {
		align-self: center;
		margin-right: 0;
		padding: 5px;
		font-weight: normal;
	}

	.filters {
		flex-direction: grid;
		grid-template-columns: repeat(3, minmax(0, 1fr));
		gap: 0.5ch;
	}

	.filters select {
		max-width: 100px;
	}

	.main-container {
		margin-top: 7em;
		grid-template-columns: repeat(2, minmax(0, 1fr));
		gap: 1ch;
		padding: 10px;
	}

	.language-tag {
	padding: 2.5px 5px;
	}
}
</style>
