<template>
    <div>
        <main class="w-screen h-screen">
            <v-map class="w-full h-full" :options="state.map" @loaded="onMapLoaded" />
            <div id="menu">
                <input id="satellite-v9" type="radio" name="rtoggle" value="satellite" checked="checked" />
                <!-- See a list of Mapbox-hosted public styles at -->
                <!-- https://docs.mapbox.com/api/maps/styles/#mapbox-styles -->
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
        <p>{{state.data}}</p>
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
        style: 'mapbox://styles/mapbox/light-v10',
        zoom: 11,
        maxZoom: 22,
        center: [-74.5447, 40.6892]
    },
    data: []
});


function onMapLoaded(map: mapboxgl.Map) {
    getGisData();
    async function getGisData() {
        state.data = await $fetch("http://localhost:3001/post-gis-backend");
        console.log("data: ", state.data);
        for (const feature of state.data) {
            console.log("this is come from loop")
            // create a HTML element for each feature
            const el = document.createElement("div");
            el.className = "marker";
            console.log("data from databse" + feature.geography);
            // make a marker for each feature and add to the map
            new mapboxgl.Marker(el)
                .setLngLat(feature.geography.coordinates)
                .addTo(map);
            console.log("line 138");
        }
    }

    map.on("dblclick", (e) => {
        new mapboxgl.Marker({
            color: "#" + (Math.random().toString(16) + "87CEEB").substring(2, 8),
            draggable: true,
        })
            .setLngLat([e.lngLat.lng, e.lngLat.lat])
            .addTo(map);
        console.log(`A click event has occurred at ${e.lngLat}`);
    });
    const layerList = document.getElementById("menu");
    const inputs = layerList.getElementsByTagName("input");
    for (const input of inputs) {
        input.onclick = (layer) => {
            const layerId = layer.target.id;
            map.setStyle("mapbox://styles/mapbox/" + layerId);
        };
    }

    //Add a vector tile source
    map.addControl(
        new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
        })
    );
    //Map Navigation Control
    map.addControl(new mapboxgl.NavigationControl(), 'top-left');

    map.addSource('wms-test-source', {
        'type': 'raster',
        // use the tiles option to specify a WMS tile source URL
        // https://docs.mapbox.com/mapbox-gl-js/style-spec/sources/
        'tiles': [
            'https://img.nj.gov/imagerywms/Natural2015?bbox={bbox-epsg-3857}&format=image/png&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&transparent=true&width=256&height=256&layers=Natural2015'
        ],
        'tileSize': 256
    });
    map.addLayer(
        {
            'id': 'wms-test-layer',
            'type': 'raster',
            'source': 'wms-test-source',
            'paint': {}
        },
        'aeroway-line'
    );
}
  //}
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

.marker {
    background-image: url("http://localhost:3000/assets/images/download.png");
    background-size: cover;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    cursor: pointer;
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
</style>