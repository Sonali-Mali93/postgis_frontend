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
        style: "mapbox://styles/mapbox/satellite-streets-v11",
        // center: [444.04931277036667, 26.266912177018096] as number[],
        zoom: 11,
        center: [-87.622088, 41.878781]
    },
    data: []
});

function onMapLoaded(map: mapboxgl.Map) {

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

    map.addLayer(
        {
            'id': 'mapillary', // Layer ID
            'type': 'line',
            'source': 'mapillary', // ID of the tile source created above
            // Source has several layers. We visualize the one with name 'sequence'.
            'source-layer': 'sequence',
            'layout': {
                'line-cap': 'round',
                'line-join': 'round'
            },
            'paint': {
                'line-opacity': 0.1,
                'line-color': 'rgb(127,255,0)',
                // 'line-color': '#7CFC00',
                'line-width': 2
            }
        },
        'road-label' // Arrange our new layer beneath this layer
    );

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
/* .w-screen {
    width: 100vw;
}

.h-screen {
    height: 100vh;
} */
/* 
.h-full {
    height: 100%;
}

.w-full {
    width: 100%;
} */

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

.marker {
    background-image: url("http://localhost:3000/assets/images/download.png");
    background-size: cover;
    width: 30px;
    height: 30px;
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
    margin-top: 600px;
}
</style>