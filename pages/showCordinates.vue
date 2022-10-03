<template>
    <div class="grid-container">
        <div class="grid-item">
            hey good morning
        </div>
        <!-- <div class="grid-item"> -->
        <main class="w-screen h-screen grid-item">
            <v-map :options="state.map" @loaded="onMapLoaded" />
            <div id="menu" class="grid-item">
                <!-- <input id="satellite-v9" type="radio" name="rtoggle" value="satellite" checked="checked" /> -->
                <!-- See a list of Mapbox-hosted public styles at -->
                <!-- https://docs.mapbox.com/api/maps/styles/#mapbox-styles -->
                <!-- <label for="satellite-v9">satellite</label>
                    <input id="light-v10" type="radio" name="rtoggle" value="light" />
                    <label for="light-v10">light</label>
                    <input id="dark-v10" type="radio" name="rtoggle" value="dark" />
                    <label for="dark-v10">dark</label>
                    <input id="streets-v11" type="radio" name="rtoggle" value="streets" />
                    <label for="streets-v11">streets</label>
                    <input id="outdoors-v11" type="radio" name="rtoggle" value="outdoors" />
                    <label for="outdoors-v11">outdoors</label> -->
            </div>
        </main>
        <!-- </div> -->
    </div>
</template>
<script setup lang="ts">
// import "mapbox-gl/dist/mapbox-gl.css";
// import "v-mapbox/dist/v-mapbox.css";
///
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import VMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import '@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';

mapboxgl.accessToken =
    "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const state = reactive({
    map: {
        container: "map",
        style: "mapbox://styles/mapbox/satellite-streets-v11",
        // center: [444.04931277036667, 26.266912177018096] as number[],
        zoom: 2,
        center: [30, 50],
    },
    data: []
});

function onMapLoaded(map: mapboxgl.Map) {

    
    //Search box
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        }),
        'top-left'
    );
    //Map Navigation Control
    map.addControl(new mapboxgl.NavigationControl(), 'top-left');

}
  //}
</script>
<style>
body {
    margin: 0;
    padding: 0;
}

#map {
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
}

.grid-container {
    display: inline-grid;
    grid-template-columns: auto auto auto;
    border: 1px solid rgba(0, 0, 0, 0.8);
    background-color: #ebedf0;
    /* padding: 5px; */
}

.grid-item {
    /* background-color: rgba(255, 255, 255, 0.8); */
    /* border: 1px solid rgba(0, 0, 0, 0.8); */
    /* padding: 35px; */
    margin-right: 20px;
    font-size: 30px;
    text-align: center;
}
</style>