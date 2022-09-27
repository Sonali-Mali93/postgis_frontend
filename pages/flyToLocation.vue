<template>
    <div>
        <main class="w-screen h-screen">
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
            <button id="fly">Fly</button>
        </main>
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
        style: 'mapbox://styles/mapbox/streets-v11',
        // center: [444.04931277036667, 26.266912177018096] as number[],
        zoom: 2,
        center: [30, 50],
        projection: 'globe'
    },
    data: []
});

function onMapLoaded(map: mapboxgl.Map) {
    map.on('style.load', () => {
        map.setFog({});
    });

    document.getElementById('fly').addEventListener('click', () => {
        // Fly to a random location
        map.flyTo({
            center: [(Math.random() - 0.5) * 360, (Math.random() - 0.5) * 100],
            essential: true // this animation is considered essential with respect to prefers-reduced-motion
        });
    });

    //Add a vector tile source
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        })
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

#fly {
    display: block;
    position: relative;
    margin: 0px auto;
    width: 50%;
    height: 40px;
    padding: 10px;
    border: none;
    border-radius: 3px;
    font-size: 12px;
    text-align: center;
    color: #fff;
    background: #ee8a65;
}
</style>