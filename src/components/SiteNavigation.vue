<template>
  <header class="sticky top-0 bg-[#1164B5] shadow-lg z-50">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">WeatherDays Project</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="toggleModal"
        ></i>
        <div v-if="!!route.params.state || !!route.params.city">
          <i
            class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
            @click="addCity"
            v-if="!!route.query"
          ></i>
        </div>

        <RouterLink :to="{ name: 'mapView' }">
          <div>
            <i class="fa-solid fa-map-location-dot"></i>
          </div>
        </RouterLink>
      </div>

      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black rounded-lg">
          <h1 class="text-2xl mb-1">About:</h1>
          <p class="mb-4">
            The WeatherDays Project will let user allow to track the current and
            future weather of cities follow users requirement.
          </p>
          <h2 class="text-2xl">How to use this application:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>
              Input city name that you want to search in to the search box to
              find the city.
            </li>
            <li>
              Select a city within the results, this will take you to the
              current weather page for your selection.
            </li>
            <li>
              Track the city by clicking on the "+" icon in the top right. This
              will save the city to view at a later time on the home page.
            </li>
          </ol>

          <h2 class="text-2xl">Removing a city</h2>
          <p>
            If you want to delete the city that already in the list, you can
            roll down to the bottom of the page to see the delete function.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from "vue-router";
import { uid } from "uid";
import { ref, watch } from "vue";
import BaseModal from "./BaseModal.vue";
import {
  doc,
  setDoc,
  getFirestore,
  collection,
  getDocs,
} from "firebase/firestore";
import { initializeApp } from "firebase/app";

const firebaseConfig = initializeApp({
  apiKey: "AIzaSyBu5xYVhh6j3jUsmb_ssmAiM0k590EcnkQ",
  authDomain: "weatherdaysproject.firebaseapp.com",
  projectId: "weatherdaysproject",
  storageBucket: "weatherdaysproject.appspot.com",
  messagingSenderId: "473677403098",
  appId: "1:473677403098:web:844e71060624f9ed8326e3",
  measurementId: "G-VD3BKC9GE3",
});

const db = getFirestore(firebaseConfig);
const savedCities = ref([]);
const route = useRoute();
const router = useRouter();

const addCity = async () => {
  const storeCity = await setDoc(doc(db, "cities", route.params.city), {
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  });

  const getCity = async () => {
    const getAvailCity = await getDocs(collection(db, "cities"));
    getAvailCity.forEach((doc) => {
      savedCities.value.push(doc.data());
    });
  };
  getCity();

  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };
  watch(savedCities.value, () => {
    savedCities.value = savedCities.value;
  });

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
