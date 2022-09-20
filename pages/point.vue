<template>
  <main class="w-screen h-screen">
    <v-map class="w-full h-full" :options="state.map" />
    <!-- Amit -->
  </main>
</template>
<script setup lang="ts">
import VMap from "v-mapbox";
const state = reactive({
  map: {
    accessToken:
      "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ",
    style: "mapbox://styles/mapbox/streets-v11?optimize=true",
    // style: "https://basemaps.cartocdn.com/gl/dark-matter-gl-style/style.json",
    center: [444.04931277036667, 26.266912177018096] as number[],
    zoom: 11,
    maxZoom: 22,
  },
});
</script>
<style>
.w-screen {
  width: 100vw;
}

.h-screen {
  height: 100vh;
}

.h-full {
  height: 100%;
}

.w-full {
  width: 100%;
}
</style>
