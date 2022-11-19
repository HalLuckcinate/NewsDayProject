<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>

    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12 w-full">
      <div
        id="mapid"
        class="block h-[450px] max-w-[650px] w-screen mb-5 rounded-lg shadow-lg -z-0"
      ></div>
      <div>
        <p class="text-md mb-5 gap-5">
          {{
            new Date(weatherData.currentTime).toLocaleDateString("en-us", {
              weekday: "short",
              day: "2-digit",
              month: "long",
            })
          }}
          {{
            new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
              timeStyle: "short",
            })
          }}
        </p>
      </div>

      <div
        class="information flex md:flex-row flex-col items-center px-[10%] py-[10px] sm:gap-[30px] justify-between rounded-lg mx-[25%] md:w-screen max-w-[885px]"
      >
        <div class="flex flex-col items-center ]">
          <h1 class="text-4xl mb-2 text-center">{{ route.params.city }}</h1>

          <p class="text-8xl mb-8">
            {{
              ((Math.round(weatherData.current.temp) - 32) * (5 / 9)).toFixed(
                1
              )
            }}&deg;C
          </p>
          <div class="text-lg text-center">
            <div class="flex flex-row justify-between gap-2">
              <div>
                <i class="fa-solid fa-temperature-low text-red-500"></i>
                Temperature:
              </div>
              <div class="">
                {{
                  (
                    (Math.round(weatherData.daily[0].temp.min) - 32) *
                    (5 / 9)
                  ).toFixed(1)
                }}
                /
                {{
                  (
                    (Math.round(weatherData.daily[0].temp.max) - 32) *
                    (5 / 9)
                  ).toFixed(1)
                }}
                &deg;C
              </div>
            </div>

            <div class="inline-block">
              <i class="fa-solid fa-droplet text-blue-600"></i>
              Humidity:
              <span class=""> {{ weatherData.current.humidity }}</span
              >%
            </div>
          </div>
        </div>
        <div
          class="flex flex-col items-center text-center min-w-[250px] text-lg"
        >
          <p>
            Feels like
            {{ (Math.round(weatherData.current.feels_like) - 32) / 2 }} &deg;C
          </p>
          <img
            class="w-[150px] h-auto"
            :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
            alt=""
          />
          <p class="capitalize">
            {{ weatherData.current.weather[0].description }}
          </p>
          <div class="flex flex-col">
            <div class="inline-block">
              <i class="fa-solid fa-wind text-blue-500"></i>
              Wind speed:
              <span class="">{{ weatherData.current.wind_speed }}</span>
              meter/s
            </div>
            <div>
              <i class="fa-solid fa-eye"></i>
              Visability:
              <span class="">{{ weatherData.current.visibility / 1000 }}</span>
              kilometer
            </div>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->
    <div class="information max-w-screen-md w-full py-10 rounded-lg">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            />
            <p class="text-xl">
              {{
                ((Math.round(hourData.temp) - 32) * (5 / 9)).toFixed(1)
              }}&deg;C
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="information mx-8 text-white p-5 rounded-lg">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <div>
              <i class="fa-solid fa-temperature-low text-red-500"></i>
              {{
                ((Math.round(day.temp.min) - 32) * (5 / 9)).toFixed(1)
              }}&deg;C/
              {{ ((Math.round(day.temp.max) - 32) * (5 / 9)).toFixed(1) }}&deg;C
            </div>
          </div>
          <div class="flex-1 flex gap-2 justify-end">
            <i class="fa-solid fa-droplet text-blue-600"></i>
            Rain: {{ !!day.rain ? day.rain : 0 }} mm
          </div>
        </div>
      </div>
    </div>
    <div class="bg-white !w-screen !max-w-[700px] !h-[550px] rounded-lg">
      <canvas id="myChart" class="!w-screen !max-w-[700px] !h-[550px]"></canvas>
    </div>

    <div
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
import { onMounted } from "vue";
import leaflet from "leaflet";
import Chart from "chart.js/auto";
import { doc, deleteDoc, getFirestore } from "firebase/firestore";
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

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const openweatherAPIKey = "7efa332cf48aeb9d2d391a51027f1a71";
const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};

const weatherData = await getWeatherData();

const dailyDay = weatherData.daily.map((day) =>
  new Date(day.dt * 1000).toLocaleDateString("en-us", {
    weekday: "long",
  })
);
const dailyTemp = weatherData.daily.map((temperature) =>
  ((Math.round(temperature.temp.max) - 32) * (5 / 9)).toFixed(1)
);

let map;

onMounted(() => {
  const ctx = document.getElementById("myChart");

  const myChart = new Chart(ctx, {
    type: "line",
    data: {
      labels: dailyDay,
      datasets: [
        {
          label: "Temperature of Week",
          data: dailyTemp,
          backgroundColor: [
            "rgba(255, 99, 132, 1)",
            "rgba(54, 162, 100, 1)",
            "rgba(255, 206, 86, 1)",
            "rgba(75, 192, 192, 1)",
            "rgba(153, 102, 255, 1)",
            "rgba(255, 159, 64, 1)",
          ],
          borderColor: ["rgba(255, 99, 132, 1)"],
          borderWidth: 1,
        },
      ],
    },
    options: {
      scales: {
        y: {
          beginAtZero: true,
        },
      },
    },
  });
  myChart;

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
    .setView([route.query.lat, route.query.lng], 10);

  const mapLayers = {
    "Cloud Map": cloudWeatherMap,
    "Sea Pressure": seaPressureWeatherMap,
    "Wind Direction": windWeatherMap,
    "Temperature Map": tempWeatherMap,
  };

  leaflet.control.layers(mapLayers).addTo(map);
});

const router = useRouter();
const removeCity = async () => {
  await deleteDoc(doc(db, "cities", route.params.city ));
  router.push({
    name: "home",
  });
};
</script>
<style lang="scss" scoped>
.information {
  background-color: #0093e9;
  background-image: linear-gradient(160deg, #0093e9 0%, #71cfac 100%);
}
</style>
