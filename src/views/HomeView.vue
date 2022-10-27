<script setup>
import { ref } from "vue";
import axios from "axios";

const mapboxAPIKey = "pk.eyJ1IjoibXVodGFzaW1mdWFkc2hvd21payIsImEiOiJja3g1amI0dDExMjk2MzBueG84d2oxOXI5In0.sOUA0Jjjb0BM1DX1FoZf4g";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);

const getSearchResults = () => {
	clearTimeout(queryTimeout.value);
	queryTimeout.value = setTimeout(async () => {
		if(searchQuery.value !== "") {
			const result = await axios.get(
				`https://api.mapbox.com/geocoding/v5/mapbox.places/
				${searchQuery.value}.json?access_token=${mapboxAPIKey}
				&type=place`
			);
			mapboxSearchResults.value = result.data.features;
			console.log("Mapbox Search Results Value: ", mapboxSearchResults.value);
		} else {
			mapboxSearchResults.value = null;
		}
	}, 300);
}
</script>

<template>
	<main class="container text-white">
		<div class="pt-4 mb-8 relative">
			<input 
			type="text"
			v-model="searchQuery"
			@input="getSearchResults"
			placeholder="Search for a city or a state" 
			class="py-2 px-1 w-full bg-transparent border-b
        	focus:border-weather-secondary focus:outline-none
        	focus:shadow-[0px_1px_0_0_#004E71]">

			<ul 
				class="absolute bg-weather-secondary text-white 
				w-full shadow-md py-2 px-1 top-[66px]"
				v-if="mapboxSearchResults"
			>
				<li 
					v-for="searchResult in mapboxSearchResults" 
					:key="searchResult.id"
					class="py-2 cursor-pointer"
				>
				{{ searchResult.place_name }}
				</li>
			</ul>
		</div>
	</main>
</template>
