<template>
  <div class="h-screen relative">
    <div id="mapid" class="h-full z-[1]"></div>
  </div>
</template>

<script setup>
import leaflet from "leaflet";
import { onMounted, ref } from "vue";

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const openweatherAPIKey = "7efa332cf48aeb9d2d391a51027f1a71";
let map;
onMounted(() => {
  // add tile layers
  const cloudWeatherMap = leaflet.tileLayer(
    `https://tile.openweathermap.org/map/clouds_new/{z}/{x}/{y}.png?appid=${openweatherAPIKey}`,
    {}
  );
  const seaPressureWeatherMap = leaflet.tileLayer(
    `https://tile.openweathermap.org/map/pressure_new/{z}/{x}/{y}.png?appid=${openweatherAPIKey}`,
    {}
  );
  const windWeatherMap = leaflet.tileLayer(
    `https://tile.openweathermap.org/map/wind_new/{z}/{x}/{y}.png?appid=${openweatherAPIKey}`,
    {}
  );
  const tempWeatherMap = leaflet.tileLayer(
    `https://tile.openweathermap.org/map/temp_new/{z}/{x}/{y}.png?appid=${openweatherAPIKey}`,
    {}
  );

  const roadMap = leaflet.tileLayer(
    `https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=${mapboxAPIKey}`,
    {
      maxZoom: 18,
      id: "mapbox/streets-v11",
      tileSize: 512,
      zoomOffset: -1,
      accessToken: mapboxAPIKey,
    }
  );
  // init map
  map = leaflet
    .map("mapid", {
      zoomControl: false,
      layers: [roadMap],
    })
    .setView([1, 1], 10);

  const mapLayers = {
    "Cloud Map": cloudWeatherMap,
    "Sea Pressure": seaPressureWeatherMap,
    "Wind Direction": windWeatherMap,
    "Temperature Map": tempWeatherMap,
  };

  leaflet.control.layers(mapLayers).addTo(map);

  // get users location
  getGeolocation();
});
const coords = ref(null);
const fetchCoords = ref(null);
const geoMarker = ref(null);
const getGeolocation = () => {
  if (!coords.value) {
    // check to see if we have coods in session sotrage
    if (sessionStorage.getItem("coords")) {
      coords.value = JSON.parse(sessionStorage.getItem("coords"));
      plotGeoLocation(coords.value);
      return;
    }
    // else get coords from geolocation API
    fetchCoords.value = true;
    navigator.geolocation.getCurrentPosition(setCoords, getLocError);
    return;
  }
  // otherwise, remove location
  coords.value = null;
  sessionStorage.removeItem("coords");
  map.removeLayer(geoMarker.value);
};
const setCoords = (pos) => {
  // stop fetching
  fetchCoords.value = null;
  // set coords in session storage
  const setSessionCoords = {
    lat: pos.coords.latitude,
    lng: pos.coords.longitude,
  };
  sessionStorage.setItem("coords", JSON.stringify(setSessionCoords));
  // set ref coords value
  coords.value = setSessionCoords;
  plotGeoLocation(coords.value);
};
const getLocError = (error) => {
  // stop fetching coords
  fetchCoords.value = null;
  geoError.value = true;
  geoErrorMsg.value = error.message;
};
const plotGeoLocation = (coords) => {
  // create custom marker

  // create new marker with coords and custom marker
  geoMarker.value = leaflet.marker([coords.lat, coords.lng]).addTo(map);
  // set map view to current location
  map.setView([coords.lat, coords.lng], 10);
};
</script>
