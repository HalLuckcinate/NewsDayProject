<template>
  <div
    class="card flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer justify-between mb-2"
  >
    <div class="flex flex-col">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
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

    <div class="flex flex-col gap-2 align-middle">
      <div class="text-2xl self-end">
        <i class="fa-solid fa-temperature-low text-red-500"></i>
        {{
          ((Math.round(city.weather.main.temp) - 32) * (5 / 9)).toFixed(1)
        }}&deg;
      </div>

      <div class="flex flex-row gap-1 items-center self-end">
        <i class="fa-solid fa-droplet text-blue-600"></i>
        Humid:
        <p>{{ Math.round(city.weather.main.humidity).toFixed(1) }}%</p>
      </div>
      <div class="flex gap-1 items-center self-end">
        <i class="fa-solid fa-wind text-blue-400"></i> Wind:
        <p>{{ Math.round(city.weather.wind.speed).toFixed(1) }} m/s</p>
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
