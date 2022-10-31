<template>
  <div
    class="card flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer justify-between"
  >
    <div class="flex flex-col">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
      <h3>
        <i class="fa-solid fa-wind text-blue-400"></i> Wind Speed:
        {{ city.weather.wind.speed }} m/s
      </h3>
    </div>

    <div class="flex flex-col items-center">
      <div>
        <img
          class="w-[50px] h-auto"
          :src="`http://openweathermap.org/img/wn/${city.weather.weather[0].icon}@2x.png`"
          alt=""
        />
      </div>
      <p class="text-xl">{{ city.weather.weather[0].main }}</p>
    </div>

    <div class="flex flex-col gap-2">
      <div class="text-2xl self-end">
        <i class="fa-solid fa-temperature-low text-red-500"></i>
        {{
          ((Math.round(city.weather.main.temp) - 32) * (5 / 9)).toFixed(1)
        }}&deg;
      </div>

      <div class="flex flex-row gap-3">
        <i class="fa-sharp fa-solid fa-droplet-percent"></i>
        Humidity:
        <p>{{ city.weather.main.humidity }}%</p>
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
<style lang="scss" scoped>
.card {
  background-color: #0093e9;
  background-image: linear-gradient(160deg, #36d1dc 0%, #5b86e5 100%);
}
</style>
