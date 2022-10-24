<template>
  <div>
    <Suspense>
      <template #default>
        <WeatherMap/>
      </template>
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import CityViewSkeleton from "../components/CityViewSkeleton.vue";
import WeatherMap from "../components/WeatherMap.vue";
</script>
