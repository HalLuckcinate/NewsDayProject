<template>
  <div
    class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer"
  >
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
      <h3><i class="fa-solid fa-wind text-blue-400"></i> Wind Speed: {{ city.weather.wind.speed }} m/s</h3>
    </div>

    <div class="flex flex-col gap-2">
      <div class="text-3xl self-end">
        <i class="fa-solid fa-temperature-low text-red-500"></i>
        {{ (Math.round(city.weather.main.temp) - 32) / 2 }}&deg;
      </div>
      <div class="flex gap-2">
        <span class="">
          Highest:
          {{ (Math.round(city.weather.main.temp_max) - 32) / 2 }}&deg;
        </span>
        <span class="">
          Lowest:
          {{ (Math.round(city.weather.main.temp_min) - 32) / 2 }}&deg;
        </span>
      </div>
      <div >
        
        Humidity:
        {{ city.weather.main.humidity }}%
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>
