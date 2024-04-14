<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from '../components/CityList.vue'
import CityCardSkelton from '../components/CityCardSkelton.vue'

const searchQuery = ref("");
const queryTimeout = ref("");
const searchError = ref(null);
const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const mapboxSearchResults = ref(null);



const router = useRouter();

const previewCity = (result) => {
  console.log(result);
  const [city, state] = result.place_name.split(",");

  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: result.geometry.coordinates[1],
      lng: result.geometry.coordinates[0],
      preview: true,
    },
  });

};

const getSearchResult = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
        // console.log(mapboxSearchResults.value);
      } catch {
        searchError.value = true;
      }

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        v-model="searchQuery"
        @input="getSearchResult"
        type="text"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004e71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <p v-if="searchError">Sorry, something get wrong, please try again.</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">
          No result match your query, try a diffrent term.
        </p>
        <template v-else>
          <li
            v-for="(res, index) in mapboxSearchResults"
            :key="index"
            class="py-2 cursor-pointer"
            @click="previewCity(res)"
          >
            {{ res.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkelton />
        <!-- <div class="flex justify-center items-center mt-6">
          <div
            class="animate-spin rounded-full h-32 w-32 border-b-2 border-white"
          ></div>
        </div> -->
      </template>
      </Suspense>
    </div>
  </main>
</template>
