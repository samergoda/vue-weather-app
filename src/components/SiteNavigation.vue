<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex felx-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i
          @click="toggleModal"
          class="fa-solid fa-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
        ></i>
        <i v-if="route.query.preview"
          @click="addCity"
          class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
        ></i>
      </div>
      <BaseModalVue
        :modalActive="modalActive"
        @close-modal="toggleModal"
        class="text-black"
        >hello from vue
        <p>
          Lorem ipsum dolor sit amet, consectetur adipisicing elit. Suscipit, id
          quibusdam? Itaque animi totam, id consequatur tenetur aperiam est
          laboriosam, architecto, deleniti labore suscipit assumenda corporis
          commodi laborum officia optio.
        </p>
      </BaseModalVue>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute ,useRouter } from "vue-router";
import { uid } from "uid";
import { ref } from "vue";
import BaseModalVue from "./BaseModal.vue";
const router = useRouter()
const route = useRoute();
const modalActive = ref(null);
const savedCities = ref([]);

const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
        
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));
  let query = Object.assign({},route.query)
  delete query.preview;
  query.id = locationObj.id
  router.replace({query})
};

const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
