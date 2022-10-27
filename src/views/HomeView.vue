<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const mapboxAPIKey = "pk.eyJ1IjoibXVodGFzaW1mdWFkc2hvd21payIsImEiOiJja3g1amI0dDExMjk2MzBueG84d2oxOXI5In0.sOUA0Jjjb0BM1DX1FoZf4g";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(false);
const router = useRouter();

const getSearchResults = () => {
	// clearing the previously set timeout
	clearTimeout(queryTimeout.value);

	// Beginning a new timeout within which to make axios calls
	queryTimeout.value = setTimeout(async () => {
		if(searchQuery.value !== "") {
			try {
				// Using Axios to collect search results using the Mapbox API
				const result = await axios.get(
				`https://api.mapbox.com/geocoding/v5/mapbox.places/
				${searchQuery.value}.json?access_token=${mapboxAPIKey}
				&type=place`
				);
				mapboxSearchResults.value = result.data.features;
			} catch {
				// Setting the application's search error state when
				// an error is caught on search
				searchError.value = true;
			}
		} else {
			mapboxSearchResults.value = null;
		}
	}, 300);
};

const previewCity = (searchResult) => {
	const placeName = searchResult.place_name.split(", ");
	const city = placeName[0];
	const state = placeName.length > 2 ? placeName[1] : 'None';
	const country = placeName[placeName.length - 1];

	// Routing based on search results
	router.push({
		name: 'cityView',
		params: {
			state: state.replaceAll(" ", ""), 
			city: city.replaceAll(" ", "")
		},
		query: {
			lat: searchResult.geometry.coordinates[1],
			lng: searchResult.geometry.coordinates[0],
			preview: true
		}
	})
};
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
				<p v-show="searchError.value">
					Sorry, something went wrong, please try again.
				</p>
				<p v-if="!searchError.value && mapboxSearchResults.length === 0">
					No results match your query, try a different term.
				</p>
				<template v-else>
					<li
						v-for="searchResult in mapboxSearchResults"
						:key="searchResult.id"
						class="py-2 cursor-pointer"
						@click="previewCity(searchResult)"
					>
					{{ searchResult.place_name }}
					</li>
				</template>
			</ul>
		</div>
	</main>
</template>
