<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";
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

const getCity = async () => {
  const getAvailCity = await getDocs(collection(db, "cities"));
  getAvailCity.forEach((doc) => {
    savedCities.value.push(doc.data());
  });
  const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
        )
      );
    });

    const weatherData = await Promise.all(requests);

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    });
};
await getCity();

const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};
</script>
