<template>
    <div>
        <main class="w-screen h-screen">

            <head>
                <link href="https://api.mapbox.com/mapbox-gl-js/v2.10.0/mapbox-gl.css" rel="stylesheet" />
            </head>
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
            <button id="fly">Fly</button>
            <div id="menu" class="">
                <input id="satellite-v9" type="radio" name="rtoggle" value="satellite" checked="checked" />
                <label for="satellite-v9">satellite</label>
                <input id="light-v10" type="radio" name="rtoggle" value="light" />
                <label for="light-v10">light</label>
                <input id="dark-v10" type="radio" name="rtoggle" value="dark" />
                <label for="dark-v10">dark</label>
                <input id="streets-v11" type="radio" name="rtoggle" value="streets" />
                <label for="streets-v11">streets</label>
                <input id="outdoors-v11" type="radio" name="rtoggle" value="outdoors" />
                <label for="outdoors-v11">outdoors</label>
            </div>
        </main>
    </div>
</template>
<script setup lang="ts">
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import vMap from "v-mapbox";
import mapboxgl from "mapbox-gl";
import geojson from "geojson";
import { once } from "events";
//import globe from "globe"
mapboxgl.accessToken =
    "pk.eyJ1Ijoic29jaWFsZXhwbG9yZXIiLCJhIjoiREFQbXBISSJ9.dwFTwfSaWsHvktHrRtpydQ";
const start = {
    center: [80, 36],
    zoom: 1,
    pitch: 0,
    bearing: 0,
};
const end = {
    // center: [74.5, 40],
    // zoom: 2
    center: [8.11862, 46.58842],
    zoom: 12.5,
    bearing: 130,
    pitch: 75,
};
//const data: string;
const state = reactive({
    map: {
        container: "map",
        zoom: 13.1,
        center: [-114.34411, 32.6141],
        //pitch: 45,
        pitch: 85,
        bearing: 80,
        // Choose from Mapbox's core styles, or make your own style with Mapbox Studio
        style: "mapbox://styles/mapbox/satellite-streets-v11",
        projection: "globe",
        ...start,
    },
});
function onMapLoaded(map: mapboxgl.Map) {
    map.setFog({
        color: "rgb(220, 159, 159)", // Pink fog / lower atmosphere
        "high-color": "rgb(36, 92, 223)", // Blue sky / upper atmosphere
        "horizon-blend": 0.4, // Exaggerate atmosphere (default is .1)
    });
    map.addSource("mapbox-dem", {
        type: "raster-dem",
        url: "mapbox://mapbox.terrain-rgb",
    });
    map.setTerrain({
        source: "mapbox-dem",
        exaggeration: 1.5,
    });
    let isAtStart = true;
    document.getElementById("fly").addEventListener("click", () => {
        // depending on whether we're currently at point a or b,
        // aim for point a or b
        const target = isAtStart ? end : start;
        isAtStart = !isAtStart;
        map.flyTo({
            ...target, // Fly to the selected target
            duration: 12000, // Animate over 12 seconds
            essential: true, // This animation is considered essential with
            //respect to prefers-reduced-motion
        });
    });
}
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

.mapboxgl-popup {
    max-width: 200px;
}

.mapboxgl-popup-content {
    text-align: center;
    font-family: "Open Sans", sans-serif;
}

#menu {
    position: absolute;
    background: #EFEFEF;
    padding: 10px;
    font-family: "Open Sans", sans-serif;
}

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
    background: #EE8A65;
}
</style>  